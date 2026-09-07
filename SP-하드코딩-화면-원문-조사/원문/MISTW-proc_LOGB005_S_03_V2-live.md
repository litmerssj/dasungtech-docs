# MISTW.dbo.proc_LOGB005_S_03_V2 실서버 전체 원문

운영 snapshot에만 존재하는 SP다. `be_anasa` develop 원문 파일이 없으므로 운영 snapshot 파일 전체를 그대로 보관한다.


원본 경로: `/Users/cigro/Desktop/anasa/anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/1321107797_proc_LOGB005_S_03_V2.sql`

```sql
-- Database: MISTW
-- Object: [dbo].[proc_LOGB005_S_03_V2]
-- Type: SQL_STORED_PROCEDURE
SET ANSI_NULLS ON;
GO
SET QUOTED_IDENTIFIER ON;
GO



/********************************************************************************
제    목 : 주문 조회
프로그램 : 패킹등록(미납패킹)
등 록 일 : 2018-05-17
등 록 자 : 양장미
수정일		수정자		내용
2021-07-20  이해권      플랙시블 품목 주문형태이 세트인 경우 품목명 앞에 (S)를 붙여서 SET 주문 품목으로 표시
2022-07-29	안진주		단가인상으로 인한 주문 미납건 증가로 인하여 주문 미납 현황 조회 기간 연장 요청으로 인하여 조회조건 수정
2023-03-30	안진주		미납현황 조회기간 기준 수정 FP : -5, 전체,SP,GP : -2
2023-09-22	박병주		Query Tunning, PDItemMaster 사용하는 컬럼만 TempTable생성 후 join (속도 이슈)
2024-05-14	안진주		미납수량이 재고량을 초과할 경우 미납수량의 색 변경 표기를 위한 select 절 내 column 추가
-----------------------------------------------------------------------
EXEC proc_LOGB005_S_03 '20181231', '20181231', 'SP', '', '', '', ''
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_LOGB005_S_03_V2]
		@iOrdYmdF			varchar(10) = ''	-- 접수일자From
	  , @iOrdYmdT			varchar(10) = ''	-- 접수일자To
	  , @iItemGrpCd	 		varchar(10) = ''	-- 품목군
	  , @iCustCd			varchar(50) = ''	-- 거래처코드
	  , @iEquipCustNm		varchar(100) = ''	-- 설비업체
	  , @iSite				varchar(100) = ''	-- 현장
	  , @iStockYn			varchar(1) = ''		-- 재고있는건
AS

BEGIN

	SET NOCOUNT ON

	--2023-09-22 박병주 추가
	select itemno, ItemNm, Spec, Unit into #tmpItem FROM MISPD.dbo.PDItemMaster with (nolock)
	
	SELECT a.CustCd							CUSTCD				-- 거래처코드
		 , dbo.fn_comCustNm(a.CustCd)		CUSTNM				-- 거래처명
		 , a.EquipCustNm					EQUIPCUSTNM			-- 설비업체
		 , a.Site							SITE				-- 현장
		 , a.DeliveryPlace					DELIVERYPLACE		-- 화물지점
		 , a.OrdRmk							ORDRMK				-- 주문비고
		 , b.ItemNo							ITEMNO				-- 품목코드
		 , CASE WHEN b.FPOrdKind = '01' THEN '(S)'+c.ItemNm ELSE c.ItemNm END AS ITEMNM				-- 품목명
		 , c.Spec							SPEC				-- 규격
		 , c.Unit							UNIT				-- 단위
		 , b.OrdQty							ORDQTY				-- 주문수량
		 , ISNULL(d.PkgQty, 0)				PKGQTY		        -- 출고수량(기패킹수량)
		 , b.OrdQty - ISNULL(d.PkgQty, 0)	DEFAULTQTY	        -- 미납수량
		 , DBO.fn_logEndStkQty('1',b.ItemNo) STKQTY             -- 재고수량
		 , a.OrdNo							ORDNO				-- 주문번호
		 , a.OrdDte							ORDDTE				-- 주문일시
		 , MISCM.dbo.fn_gfnDateTypeTrans(a.DeliveryReqYmd, '-')	DELIVERYREQYMD		-- 납기요청일
		 , ''								SHIPMENTYMD			-- 출하예정일
		 , dbo.fn_comEmpNm(a.OrdEmpNo)		ORDEMPNM			-- 주문자명
		 , DATEDIFF(d, CONVERT(datetime, a.DeliveryReqYmd, 112), GETDATE())	ELAPSEDDAYS	-- 경과일수
		 , 'N'								PKGYN				-- 패킹여부
		 , dbo.fn_logEndStkQty(1, b.ItemNo)	STOCKQTY			-- 재고량
		 , b.Hyeopga						HYEOPGA				-- 협가
		 , b.NegoRate						NEGORATE			-- 네고율
		 , b.OrdEnrDtlNo					ORDENRDTLNO			-- 주문등록상세번호
		 , a.OrdEnrNo						ORDENRNO			-- 주문등록번호
		 , a.ItemGrpCd						ITEMGRPCD			-- 품목군
		 , (SELECT ADR  FROM MISTW.dbo.tbLogOrdEnr with (nolock) WHERE A.OrdEnrNo  = OrdEnrNo) AS ADR --주소
		 -- 20240514 미납수량이 재고량을 초과할 경우 화면 내 글씨 색 변경 요청건으로 인한 구분값 추가
		 , CASE WHEN (b.OrdQty - ISNULL(d.PkgQty, 0)) > DBO.fn_logEndStkQty('1',b.ItemNo) THEN 'Y' ELSE 'N' END AS OVERQTY -- 미납수량 재고량 차이		 
	INTO   #Temp
	FROM   MISTW.dbo.tbLogOrdEnr a with (nolock)
	INNER JOIN MISTW.dbo.tbLogOrdEnrDtl b with (nolock) ON b.OrdEnrNo = a.OrdEnrNo
	--2023-09-22 박병주 추가
--	INNER JOIN MISPD.dbo.PDItemMaster c with (nolock) ON c.ItemNo = b.ItemNo
	INNER JOIN #tmpItem c ON c.ItemNo = b.ItemNo
	LEFT JOIN (SELECT x.OrdEnrDtlNo, SUM(ISNULL(y.OutQty, x.pkgQty)) PkgQty
			   FROM   MISTW.dbo.tbLogPkgEnrDtl x with (nolock)
			   LEFT JOIN MISTW.dbo.tbLogOutDtl y with (nolock)
			     ON y.pkgEnrDtlNo = x.pkgEnrDtlNo
			   GROUP BY x.OrdEnrDtlNo) d ON d.OrdEnrDtlNo = b.OrdEnrDtlNo
	--2022/07/29 단가인상 작업으로 미납주문건 증가 => 사용자 요청에 의하여 조회 기준일자 수정
	--2023/03/29 FP는 다량 주문 후 3개월 이상 분할하여 납품하는 경우가 있어 FP를 제외한 나머지 품목군 기한 복구 요청으로 수정처리
	--WHERE  a.DeliveryReqYmd BETWEEN  CONVERT(VARCHAR(8),CONVERT(DATETIME,CONVERT(VARCHAR(6),DATEADD(M, -2, CONVERT(datetime, @iOrdYmdF)),112)+'01'),112)  AND @iOrdYmdF	
	--WHERE  a.DeliveryReqYmd BETWEEN  CONVERT(VARCHAR(8),CONVERT(DATETIME,CONVERT(VARCHAR(6),DATEADD(M, -5, CONVERT(datetime, @iOrdYmdF)),112)+'01'),112)  AND @iOrdYmdF
	WHERE  a.DeliveryReqYmd BETWEEN REPLACE(@iOrdYmdF, '-', '') AND REPLACE(@iOrdYmdT, '-', '')
	AND    ISNULL(B.OutStat,'')     NOT IN ('04','99')  --출고완료 및 주문취소 제외
	AND    a.OrdType NOT IN ('02','03')				-- 반품은 패킹하지 않음
	AND    ISNULL(b.OrdItemRecYn, '') = 'Y'
	AND    ISNULL(B.CloseYn,'') <> 'Y'
	AND    (b.OrdQty - ISNULL(d.PkgQty, 0)) <> 0
	AND   ( @iItemGrpCd        is null or @iItemGrpCd        = '%' or  ( @iItemGrpCd        Is not null and @iItemGrpCd        <> '%' and a.ItemGrpCd = @iItemGrpCd ))
	AND   ( @iCustCd       is null or @iCustCd       = ''  or  ( @iCustCd       is not null and @iCustCd       <> '' and A.CustCd             =  @iCustCd ))
	AND   ( @iEquipCustNm  is null or @iEquipCustNm  = ''  or  ( @iEquipCustNm  is not null and @iEquipCustNm  <> '' and A.EquipCustNm        LIKE  '%'+  @iEquipCustNm+'%' ))	
	ORDER BY C.ITEMNM,C.SPEC,a.DeliveryReqYmd ASC  

	IF @iStockYn = 'Y'
	BEGIN
		DELETE FROM #Temp WHERE OrdEnrDtlNo IN (SELECT OrdEnrDtlNo FROM #Temp WHERE STOCKQTY <= 0)		
	END
	
	
	SELECT 'N'	CHK
		 , CUSTCD			-- 거래처코드
		 , CUSTNM			-- 거래처명
		 , EQUIPCUSTNM		-- 설비업체
		 , SITE				-- 현장
		 , DELIVERYPLACE	-- 화물지점
		 , ORDRMK			-- 주문비고
		 , ITEMNO			-- 품목코드
		 , ITEMNM			-- 품목명
		 , SPEC				-- 규격
		 , UNIT				-- 단위
		 , ORDQTY			-- 주문수량
		 , PKGQTY			-- 출고수량(기패킹수량)
		 , DEFAULTQTY		-- 미납수량
		 , ORDNO			-- 주문번호
		 , ORDDTE			-- 주문일시
		 , DELIVERYREQYMD	-- 납기요청일
		 , SHIPMENTYMD		-- 출하예정일
		 , ORDEMPNM			-- 주문자명
		 , ELAPSEDDAYS		-- 경과일
		 , PKGYN			-- 패킹여부
		 , STOCKQTY			-- 재고량
		 , HYEOPGA			-- 협가
		 , NEGORATE			-- 네고율
		 , ORDENRDTLNO		-- 주문등록상세번호
		 , ORDENRNO			-- 주문등록번호
		 , ITEMGRPCD		-- 품목군
		 , ADR              -- 주소
		 -- 20240514 미납수량이 재고량을 초과할 경우 화면 내 글씨 색 변경 요청건으로 인한 구분값 추가
		 , CASE WHEN DEFAULTQTY > STOCKQTY THEN 'Y' ELSE 'N' END AS OVERQTY -- 미납수량 재고량 차이		 
	FROM   #Temp
	ORDER BY ITEMNM,SPEC,DELIVERYREQYMD ASC



END
GO
```

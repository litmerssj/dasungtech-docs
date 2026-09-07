# MISTO SP 전체 원문

우선 조사 대상 SP 4개. 각 SP의 화면·route 호출 정보와 원문 상태를 머리말에 적고, 아래 fenced code block은 선택한 원문 파일 전체다.

| SP | 화면/route 수 | 원문 기준 |
| --- | ---: | --- |
| [`str_TMSTA00300_S`](#misto-str-tmsta00300-s) | 0 | be_anasa develop 원문 |
| [`str_TMSTA00600_S`](#misto-str-tmsta00600-s) | 0 | be_anasa develop 원문 |
| [`str_TMSTA00601_S`](#misto-str-tmsta00601-s) | 0 | be_anasa develop 원문 |
| [`str_TMDEL00800_S`](#misto-str-tmdel00800-s) | 0 | be_anasa develop 원문 |

<a id="misto-str-tmsta00300-s"></a>
## `MISTO.dbo.str_TMSTA00300_S`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTO/str_TMSTA00300_S.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTO/str_TMSTA00300_S.sql

```sql


/********************************************************************************
제    목 : 타입별 임대공구 현황
프로그램 : 
등 록 일 : 2016-02-01
등 록 자 : 김재영
수정일		수정자		내용
-----------------------------------------------------------------------
EXEC MISTO.dbo.str_TMSTA00300_S @iFRYMD = '20151228'
EXEC MISTO.dbo.str_TMSTA00300_S @iFRYMD = '20181017', @iItemGrpCd = 'GP'
*********************************************************************************/
CREATE PROCEDURE [dbo].[str_TMSTA00300_S]
	@iFRYMD     VARCHAR(8)            --작성일자
   ,@iItemGrpCd VARCHAR(10)           --품목군
AS

BEGIN

	SET NOCOUNT ON;

	IF @iItemGrpCd ='SP'
		BEGIN
			SELECT CLASSMNM
				, SUM(CASE WHEN TOOLNM = '죠우13' THEN QTY ELSE  0 END) [J013]
				, SUM(CASE WHEN TOOLNM = '죠우20' THEN QTY ELSE  0 END) [J020] 
				, SUM(CASE WHEN TOOLNM = '죠우25' THEN QTY ELSE  0 END) [J025]
				, SUM(CASE WHEN TOOLNM = '죠우30' THEN QTY ELSE  0 END) [J030]
				, SUM(CASE WHEN TOOLNM = '죠우40' THEN QTY ELSE  0 END) [J040]
				, SUM(CASE WHEN TOOLNM = '죠우50' THEN QTY ELSE  0 END) [J050]
				, SUM(CASE WHEN TOOLNM = '죠우60' THEN QTY ELSE  0 END) [J060]
				, SUM(CASE WHEN TOOLNM = '죠우75' THEN QTY ELSE  0 END) [J075]
				, SUM(CASE WHEN TOOLNM = '죠우80' THEN QTY ELSE  0 END) [J080]
				, SUM(CASE WHEN TOOLNM = '죠우100' THEN QTY ELSE  0 END) [J100]		
				, SUM(CASE WHEN TOOLNM = '클램핑툴중형(구형)' THEN QTY ELSE  0 END) [CLAMPM]
				, SUM(CASE WHEN TOOLNM = '클램핑툴중형(신형)' THEN QTY ELSE  0 END) [CLAMPL]
				, SUM(CASE WHEN TOOLNM = '클램핑툴대형(구형)' THEN QTY ELSE  0 END) [BIGCLAMPM]
				, SUM(CASE WHEN TOOLNM = '클램핑툴대형(신형)' THEN QTY ELSE  0 END) [BIGCLAMPL]
				, SUM(CASE WHEN TOOLNM = '실린더중형' THEN QTY ELSE  0 END) [CYLINDERM]
				, SUM(CASE WHEN TOOLNM = '실린더대형' THEN QTY ELSE  0 END) [CYLINDERL]
				, SUM(CASE WHEN TOOLNM = '충전건구형' THEN QTY ELSE  0 END) [GUNM]
				, SUM(CASE WHEN TOOLNM = '충전건신형' THEN QTY ELSE  0 END) [GUNL]
				, SUM(CASE WHEN TOOLNM = '펌프' THEN QTY ELSE  0 END) [PUMP]
				, SUM(CASE WHEN TOOLNM = '절단기' THEN QTY ELSE  0 END) [CUTTER]
				--20161025 강윤철 타입별 임대현황에 대형건으로의 조회수량 추가
				, SUM(CASE WHEN TOOLNM = '대형건' THEN QTY ELSE  0 END) [LGUN]
				, COUNT(*) AS 합계
				FROM (
					SELECT CASE WHEN C.CLASSMNM  IN ('롱타입', '체인식') THEN  C.CLASSMNM ELSE '힌지형'  END CLASSMNM				
						, C.SPEC
						, DD.QTY
						, CASE WHEN C.CLASSLNM = '죠우' AND C.SPEC = '13' THEN '죠우13'
							WHEN C.CLASSLNM = '죠우' AND C.SPEC = '20' THEN '죠우20'
							WHEN C.CLASSLNM = '죠우' AND C.SPEC = '25' THEN '죠우25'
							WHEN C.CLASSLNM = '죠우' AND C.SPEC = '30' THEN '죠우30'
							WHEN C.CLASSLNM = '죠우' AND C.SPEC = '40' THEN '죠우40'
							WHEN C.CLASSLNM = '죠우' AND C.SPEC = '50' THEN '죠우50'
							WHEN C.CLASSLNM = '죠우' AND C.SPEC = '60' THEN '죠우60'						
							WHEN C.CLASSLNM = '죠우' AND C.SPEC = '80' THEN '죠우80'
							WHEN C.CLASSLNM = '죠우' AND C.SPEC = '100' THEN '죠우100'
							WHEN C.CLASSLNM = '죠우' AND C.SPEC = '75' THEN '죠우75'
							WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '중형' AND C.CLASSSCD = '01' THEN '클램핑툴중형(구형)'
							WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '중형' AND C.CLASSSCD = '02' THEN '클램핑툴중형(신형)'
							WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '대형' AND C.CLASSSCD = '01' THEN '클램핑툴대형(구형)'
							WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '대형' AND C.CLASSSCD = '02' THEN '클램핑툴대형(신형)'
							WHEN C.CLASSLNM = '실린더' AND C.SPEC = '중형' THEN '실린더중형'
							WHEN C.CLASSLNM = '실린더' AND C.SPEC = '대형' THEN '실린더대형'
							WHEN C.CLASSLNM = '충전건' AND C.CLASSSNM = '구형' THEN '충전건구형'
							WHEN C.CLASSLNM = '충전건' AND C.CLASSSNM = '신형' THEN '충전건신형'
							WHEN C.CLASSLNM = '전동펌프' THEN '펌프'
							WHEN C.CLASSLNM = '절단기' THEN '절단기'
							--20161025 강윤철 타입별 임대현황에 대형건으로의 조회수량 추가
							WHEN C.GUBUN1 = '건' AND C.GUBUN2 = '대형' THEN '대형건'
					ELSE '' END TOOLNM
					  FROM TODELIVERYMASTER DM
					  JOIN TODELIVERYDETAIL DD ON DD.DELIVERYNO = DM.DELIVERYNO AND DD.ItemGrpCd = @iItemGrpCd AND DD.ISSUEYMD <= @iFRYMD
					  JOIN TOToolMaster TM ON TM.LotNo = dd.LotNo AND TM.ItemGrpCd = @iItemGrpCd
					  JOIN TOClass C ON C.ClassCd = TM.ClassCd AND C.ItemGrpCd = @iItemGrpCd					
					 WHERE DM.DELIVERYGB IN ('01','03')
					   AND DM.DeliveryYmd <= @iFRYMD
					   and DM.ItemGrpCd = @iItemGrpCd
					   AND NOT EXISTS (	SELECT 1
											  FROM TORECOVERY X
											 WHERE X.DELIVERYNO = DD.DELIVERYNO
											   AND X.SEQ = DD.SEQ
											   AND X.ItemGrpCd = @iItemGrpCd
											   AND x.RecoveryYmd <= @iFRYMD)
					   AND NOT EXISTS (	select 1
										  FROM ToLoss x
										 WHERE x.DeliveryNo = DD.DeliveryNo
										   AND x.Seq = DD.Seq
										   AND X.ItemGrpCd = @iItemGrpCd
										   AND x.CancelYN != 'Y'
										   AND x.LossYmd <= @iFRYMD
										   )
						-- 20170823 강윤철 폐기는 회수 후 처리하는 것으로 임대수량과는 관계 없어(테이블에도 출고관련 정보가 없다) 주석처리 - 회수 후 처리되므로 재고수량에는 관계있음
						----20161025 강윤철 TMSTA00400-대리첨별출고현황의 임대로 조회시 수량과 맞추기 위해 추가
						--AND NOT EXISTS (select 1
						--		FROM TODisUse x
						--	 WHERE x.LotNo = DD.LotNo
						--	   AND x.DisUseYmd <= @iFRYMD)
						) A
				GROUP BY CLASSMNM
				order by CLASSMNM  desc


			SELECT SUM(CASE WHEN ClassSCd='01' THEN (DD.QTY) ELSE 0 END) OLD
			  ,SUM(CASE WHEN ClassSCd='02' THEN (DD.QTY) ELSE 0 END) NEW
			  ,C.SPEC+'수' SPEC
			  FROM TODELIVERYMASTER DM
				JOIN TODELIVERYDETAIL DD ON DD.DELIVERYNO = DM.DELIVERYNO AND DD.ItemGrpCd = @iItemGrpCd AND DD.ISSUEYMD <= @iFRYMD
				JOIN TOToolMaster TM ON TM.LotNo = dd.LotNo AND TM.ItemGrpCd = @iItemGrpCd
				JOIN TOClass C ON C.ClassCd = TM.ClassCd AND C.ItemGrpCd = @iItemGrpCd
				WHERE DM.DELIVERYGB IN ('01','03')
				AND DM.DeliveryYmd <= @iFRYMD
				AND ClassLCd='10' AND ClassMCd='20' AND C.SPEC IN('30','40','50','60','75','80','100')
				AND DM.ItemGrpCd = @iItemGrpCd
				AND NOT EXISTS (	SELECT 1
										FROM TORECOVERY X
										WHERE X.DELIVERYNO = DD.DELIVERYNO
										AND X.SEQ = DD.SEQ
										AND X.ItemGrpCd = @iItemGrpCd
										AND x.RecoveryYmd <= @iFRYMD)
				AND NOT EXISTS (	select 1
									FROM ToLoss x
									WHERE x.DeliveryNo = DD.DeliveryNo
									AND x.Seq = DD.Seq
									AND X.ItemGrpCd = @iItemGrpCd
									AND x.CancelYN != 'Y'
									AND x.LossYmd <= @iFRYMD
									) 	   
			 GROUP BY CLASSMNM,C.SPEC
			 ORDER BY CONVERT(INT,C.SPEC)
		END
	ELSE IF @iItemGrpCd ='GP'	
		BEGIN
			SELECT     
				  ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '상단롤러상단25'				THEN DD.QTY ELSE 0 END), 0)	AS LOUP025
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '상단롤러상단32~40'			THEN DD.QTY ELSE 0 END), 0)	AS LOUP032
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '상단롤러상단50~150'			THEN DD.QTY ELSE 0 END), 0)	AS LOUP050
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '상단롤러상단100~150(전용)'	THEN DD.QTY ELSE 0 END), 0)	AS LOUP100
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '상단롤러상단200~300'		THEN DD.QTY ELSE 0 END), 0)	AS LOUP200
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '하단롤러하단25'				THEN DD.QTY ELSE 0 END), 0)	AS LODW025
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '하단롤러하단32~40'			THEN DD.QTY ELSE 0 END), 0)	AS LODW032
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '하단롤러하단50~150'			THEN DD.QTY ELSE 0 END), 0)	AS LODW050
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '하단롤러하단100~150(전용)'	THEN DD.QTY ELSE 0 END), 0)	AS LODW100
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '하단롤러하단200~300'		THEN DD.QTY ELSE 0 END), 0)	AS LODW200
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '샤프트상단'				THEN DD.QTY ELSE 0 END), 0)	AS SPUP
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '샤프트하단'				THEN DD.QTY ELSE 0 END), 0)	AS SPDW
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '펌프'						THEN DD.QTY ELSE 0 END), 0)	AS PUMP
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '그루브가공기'				THEN DD.QTY ELSE 0 END), 0)	AS GROV
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '파이프지지대'				THEN DD.QTY ELSE 0 END), 0)	AS PIPE
				
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '그루브가공기' AND C.Spec = 'DY'		THEN DD.QTY ELSE 0 END), 0)	AS GROV_DY
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '그루브가공기' AND C.Spec = 'DS'		THEN DD.QTY ELSE 0 END), 0)	AS GROV_DS
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '펌프' AND C.Spec = 'DS'	THEN DD.QTY ELSE 0 END), 0)	AS PUMP_DS
				, ISNULL(SUM(CASE WHEN MISTW.DBO.fn_GtcClassNM(C.ItemGrpCd, C.ClassCd) = '펌프' AND C.Spec = 'SS'	THEN DD.QTY ELSE 0 END), 0)	AS PUMP_SS

				, ISNULL(SUM(DD.QTY), 0)			 																							AS TOTAL_CNT
			FROM MISTO..TODELIVERYMASTER DM
			JOIN MISTO..TODELIVERYDETAIL DD ON DD.DELIVERYNO = DM.DELIVERYNO AND DD.IssueYmd <= @iFRYMD
			JOIN MISTO..TOToolMaster TM ON TM.LotNo = dd.LotNo AND TM.ItemGrpCd = DD.ItemGrpCd
			JOIN MISTO..TOClass C ON C.ClassCd = TM.ClassCd AND C.ItemGrpCd = TM.ItemGrpCd
			WHERE DM.DELIVERYGB IN ('01','03')	--임대, AS
			AND DM.DeliveryYmd <= @iFRYMD
			AND DM.ItemGrpCd = @iItemGrpCd
			AND NOT EXISTS (SELECT 1
							FROM MISTO..TORECOVERY X
							WHERE X.DELIVERYNO = DD.DELIVERYNO
							AND X.SEQ = DD.SEQ
							AND X.ItemGrpCd = @iItemGrpCd
							AND x.RecoveryYmd <= @iFRYMD)
			AND NOT EXISTS (select 1
							FROM MISTO..ToLoss x
							WHERE x.DeliveryNo = DD.DeliveryNo
							AND x.Seq = DD.Seq
							AND X.ItemGrpCd = @iItemGrpCd
							AND x.CancelYN != 'Y'
							AND x.LossYmd <= @iFRYMD
							) 		



			--SELECT SUM(CASE WHEN ClassSCd='01' THEN (DD.QTY) ELSE 0 END) OLD
			--  ,SUM(CASE WHEN ClassSCd='02' THEN (DD.QTY) ELSE 0 END) NEW
			--  ,C.SPEC+'수' SPEC
			--  FROM TODELIVERYMASTER DM
			--	JOIN TODELIVERYDETAIL DD ON DD.DELIVERYNO = DM.DELIVERYNO AND DD.ItemGrpCd = @iItemGrpCd AND DD.ISSUEYMD <= @iFRYMD
			--	JOIN TOToolMaster TM ON TM.LotNo = dd.LotNo AND TM.ItemGrpCd = @iItemGrpCd
			--	JOIN TOClass C ON C.ClassCd = TM.ClassCd AND C.ItemGrpCd = @iItemGrpCd
			--	WHERE DM.DELIVERYGB IN ('01','03')
			--	AND DM.DeliveryYmd <= @iFRYMD
			--	AND ClassLCd='10' AND ClassMCd='20' AND C.SPEC IN('30','40','50','60','75','80','100')
			--	AND DM.ItemGrpCd = @iItemGrpCd
			--	AND NOT EXISTS (	SELECT 1
			--							FROM TORECOVERY X
			--							WHERE X.DELIVERYNO = DD.DELIVERYNO
			--							AND X.SEQ = DD.SEQ
			--							AND X.ItemGrpCd = @iItemGrpCd
			--							AND x.RecoveryYmd <= @iFRYMD)
			--	AND NOT EXISTS (	select 1
			--						FROM ToLoss x
			--						WHERE x.DeliveryNo = DD.DeliveryNo
			--						AND x.Seq = DD.Seq
			--						AND X.ItemGrpCd = @iItemGrpCd
			--						AND x.CancelYN != 'Y'
			--						AND x.LossYmd <= @iFRYMD
			--						) 	   
			-- GROUP BY C.SPEC
			-- ORDER BY CONVERT(INT,C.SPEC)
		END
END
```

<a id="misto-str-tmsta00600-s"></a>
## `MISTO.dbo.str_TMSTA00600_S`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTO/str_TMSTA00600_S.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTO/str_TMSTA00600_S.sql

```sql

/********************************************************************************
제    목 : 주간업무보고용 조회
프로그램 : 주간업무보고용 조회
등 록 일 : 2016-02-02
등 록 자 : 김재환
수정일		수정자		내용
-----------------------------------------------------------------------
20161219	강윤철		SetCode 없는 계약(이전자료 - 20160401이전)의 [기간별 임대현황]에서 수량파악위해 충전식, 유압식 체크추가
20170203	강윤철		조회대상 SETCODE변경
20170203	강윤철		사용자 요청으로 반납에서는 이동건도 포함되도록 수정
20170203	강윤철		사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
20170210	강윤철		20170203요청한 반납 시 이동건 포함되지만 해당 기간내에 일어난 이동건은 제외되도록 수정요청
20170410	양장미		공구이동처리 건 중 이동 후 생성된 건은 포함되도록
20170414	강윤철		(0410건 재수정)이동이 다 빠지는 것이 맞고 일부품목의 경우만 안빠지도록 체크로직 필요
20170526	강윤철		분실처리로 계약 종료된 건은 반납카운트 하지 않도록 수정
20220726	안진주		사용자 요청으로 이동이력이 있는 주문건이라도 실반납(AS, 계약종료)가 있으면 해당 내용은 조회되게 수정
20230330	안진주		최종적으로 정리 된 조회 요청 내용 기준으로 반납 이력 확인할 수 있게 수정처리

EXEC [str_TMSTA00600_S] '20170101', '20170109'
*********************************************************************************/
CREATE PROCEDURE [dbo].[str_TMSTA00600_S]
	  @iYMD_F VARCHAR(8)		-- 년월
	, @iYMD_T VARCHAR(8)		-- 년월
AS
DECLARE @iSUM float = 0
	 
BEGIN
	SET NOCOUNT ON;


	SELECT CASE WHEN DM.RentGb = '01' THEN '신규' ELSE '추가' END Gubn
		 , CASE WHEN CM.SetCode IN ('01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60','90', 'A','D') THEN '충전식' ELSE '유압식' END SetCode
		-- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
		--,	CASE WHEN CM.SetCode IN ('A','P') THEN '충전식' ELSE '유압식' END AS SETCODE
		 , COUNT(*) Cnt
	INTO   #TEMP
	FROM   MISTO..TODeliveryMaster DM
	INNER JOIN MISTO..TOContractMaster CM ON CM.ContractNo = DM.ContractNo
	WHERE  DM.DeliveryGb = '01'
	AND		DM.ItemGrpCd = 'SP'
	AND    DM.DeliveryYmd BETWEEN @iYMD_F AND @iYMD_T
	-- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
	AND    CM.CustCd != 'CG001'

	-- 20190116 김은기 오영선씨 요청으로 대상 SETCODE변경
	 AND    CM.SetCode IN (select ClassGrpCd FROM MISTO..TOSet A where ItemGrpCd ='SP' and chargeyn IN ('Y','N'))
	-- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
	--AND		CM.SetCode IN ('A','P','M','B','Q','R')
	AND    ISNULL(DM.RentGb, '') != ''
	AND    NOT EXISTS(SELECT 1
					  FROM   MISTO..TOMove
					  -- 20170410 양장미 공구이동처리 건 중 이동 후 생성된 건은 포함되도록
					   -- 20170414 강윤철 (0410건 재수정)이동이 다 빠지는 것이 맞고 일부품목의 경우만 안빠지도록 체크로직 필요
					   --WHERE (DeliveryMoveNo = DM.DeliveryNo OR DeliveryNo = DM.DeliveryNo)
					  --AND    LEFT(MoveNo, 8) BETWEEN @iYMD_F AND @iYMD_T)
					  --20170420 재수정 - 공구이동처리건 중 이동 후 생성된 건은 제외 => 이동 전 원건만 포함되도록
					  WHERE  DeliveryMoveNo = DM.DeliveryNo
					  AND    ISNULL(PartMoveFl ,'') <> 'Y')
	GROUP BY CASE WHEN DM.RentGb = '01' THEN '신규' ELSE '추가' END
		   , CASE WHEN CM.SetCode IN ( '01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60','90', 'A','D') THEN '충전식' ELSE '유압식' END 
	UNION ALL
   SELECT '반납' GUBN
       , CASE WHEN SetCode IN( '01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60','90', 'A','D')THEN '충전식' ELSE '유압식' END SETCODE
      -- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
      --,   CASE WHEN CM.SetCode IN ('A','P') THEN '충전식' ELSE '유압식' END AS SETCODE
       , COUNT(*) CNT

   --FROM   TODeliveryMaster DM
   --INNER JOIN TOContractMaster CM ON CM.ContractNo = DM.ContractNo
   --20170424 이동 건 반납 시 최종 이동 건의 반납일자로 Count
   FROM 
      (
         SELECT CM.ContractNo, CM.SetCode from  
               (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo 
                        FROM MISTO..TODeliveryMaster A 
                     LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
               INNER JOIN MISTO..TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
			   INNER JOIN TODeliveryDetail DD ON DD.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) 
			   INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo, RecoveryYmd 
						     FROM TORecovery 
							WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R 
			   ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = DD.LotNo		
               WHERE  CM.ContractGb = '03'
               AND      CM.ItemGrpCd = 'SP'
               --AND    CM.ReturnYmd BETWEEN @iYMD_F AND @iYMD_T
               -- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
               AND    CM.CustCd != 'CG001'
               AND    CM.SetCode IN (    select ClassGrpCd FROM MISTO..TOSet A where ItemGrpCd ='SP' and chargeyn IN ('Y','N'))
               -- 20170526   강윤철   분실처리로 계약 종료된 건은 반납카운트 하지 않도록 수정
			   AND	  DD.LotNo NOT IN (SELECT LotNo FROM MISTO..ToLoss WHERE DeliveryNo = DD.DeliveryNo AND LotNo = DD.LotNo AND Seq = DD.Seq AND CancelYN <> 'Y')
				-- 20230120 고종수 이동, 교환의 이력이 있더라도 최종적으로 실반납이 된 경우
				AND		NOT EXISTS (SELECT	1
									FROM	TOMove	X
									LEFT	JOIN    TORecovery Y
									ON		X.DeliveryNo = Y.DeliveryNo
									WHERE	X.DeliveryNo = DM.DeliveryNo
									AND		DD.LotNo = Y.LotNo
									AND     Y.DeliveryNo is null)				
				-- 20230120 고종수 출고번호 기준 분실 된 이력이 있더라도 실제 분실된 Lot를 제외한 나머지 Lot가 정상 반납이 된 경우
				AND		NOT EXISTS (SELECT 1
									FROM MISTO..ToLoss a 
									LEFT JOIN TORecovery b
									ON a.deliveryno = b.DeliveryNo
									AND a.LotNo = b.LotNo
									WHERE a.DeliveryNo = dm.DeliveryNo
									AND a.LotNo = DD.LotNo
									AND a.CancelYN ='N'
									AND b.DeliveryNo is null
									)		

      union 
      
         SELECT CM.ContractNo, CM.SetCode from  
               (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo 
                        FROM MISTO..TODeliveryMaster A 
                     LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
               INNER JOIN MISTO..TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
			   INNER JOIN TODeliveryDetail DD ON DD.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) 
			   INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo, RecoveryYmd 
						     FROM TORecovery 
							WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R 
			   ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = DD.LotNo		
               WHERE  CM.ContractGb = '03'
               AND      CM.ItemGrpCd = 'SP'
               --AND    CM.ReturnYmd BETWEEN @iYMD_F AND @iYMD_T
               -- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
               AND    CM.CustCd != 'CG001'
               AND    CM.SetCode IN (    select ClassGrpCd FROM MISTO..TOSet A where ItemGrpCd ='SP' and chargeyn IN ('Y','N'))               
			   AND	  DD.LotNo NOT IN (SELECT LotNo FROM MISTO..ToLoss WHERE DeliveryNo = DD.DeliveryNo AND LotNo = DD.LotNo AND Seq = DD.Seq AND CancelYN <> 'Y')
			   AND	  EXISTS (SELECT 1 FROM TOAs TS WHERE TS.DeliveryNo = DM.DeliveryNo)
         ) AA

   --AND EXISTS (SELECT 1 FROM TOAs t WHERE t.DeliveryNo = DM.DeliveryNo)
   GROUP BY CASE WHEN SetCode IN( '01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60','90', 'A','D')THEN '충전식' ELSE '유압식' END
	UNION ALL
	SELECT '반납'
		 , '충전식'
		 , COUNT(CM.ContractNo)
	--FROM   TODeliveryMaster DM
	--INNER JOIN TOContractMaster CM ON CM.ContractNo = DM.ContractNo
	--INNER JOIN (SELECT DISTINCT DeliveryNo FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R ON R.DeliveryNo = DM.DeliveryNo
	--20170424 이동 건 반납 시 최종 이동 건의 반납일자로 Count
	FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM MISTO..TODeliveryMaster A LEFT JOIN MISTO..TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
	INNER JOIN MISTO..TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
	INNER JOIN TODeliveryDetail DD ON DD.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) 
	INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo, RecoveryYmd 
				  FROM TORecovery 
				 WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R 
				    ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = DD.LotNo		
	WHERE  ISNULL(CM.ChargerTypeFL, '') = 'Y'
	AND	    CM.ItemGrpCd = 'SP'
	AND     CM.CustCd != 'CG001'	
	AND	    DD.LotNo NOT IN (SELECT LotNo FROM MISTO..ToLoss WHERE DeliveryNo = DD.DeliveryNo AND LotNo = DD.LotNo AND Seq = DD.Seq AND CancelYN <> 'Y')
	-- 20230120 고종수 이동, 교환의 이력이 있더라도 최종적으로 실반납이 된 경우
	AND		NOT EXISTS (SELECT	1
						FROM	TOMove	X
						LEFT	JOIN    TORecovery Y
						ON		X.DeliveryNo = Y.DeliveryNo
						WHERE	X.DeliveryNo = DM.DeliveryNo
						AND		DD.LotNo = Y.LotNo
						AND     Y.DeliveryNo is null)				
	-- 20230120 고종수 출고번호 기준 분실 된 이력이 있더라도 실제 분실된 Lot를 제외한 나머지 Lot가 정상 반납이 된 경우
	AND		NOT EXISTS (SELECT 1
						FROM MISTO..ToLoss a 
						LEFT JOIN TORecovery b
						ON a.deliveryno = b.DeliveryNo
						AND a.LotNo = b.LotNo
						WHERE a.DeliveryNo = dm.DeliveryNo
						AND a.LotNo = DD.LotNo
						AND a.CancelYN ='N'
						AND b.DeliveryNo is null
						)			 

	UNION ALL
	SELECT '반납'
		 , '유압식'
		 , COUNT(CM.ContractNo)
	--FROM   TODeliveryMaster DM
	--INNER JOIN TOContractMaster CM ON CM.ContractNo = DM.ContractNo
	--INNER JOIN (SELECT DISTINCT DeliveryNo FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R ON R.DeliveryNo = DM.DeliveryNo
	--20170424 이동 건 반납 시 최종 이동 건의 반납일자로 Count
	FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM MISTO..TODeliveryMaster A LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = MISTO.dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
	INNER JOIN MISTO..TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)	
	INNER JOIN TODeliveryDetail DD ON DD.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) 
	INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo, RecoveryYmd 
				  FROM TORecovery 
				 WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R 
				    ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = DD.LotNo		
	WHERE	ISNULL(CM.OilTypeFL, '') = 'Y'
	AND		CM.ItemGrpCd = 'SP'
	AND		CM.CustCd != 'CG001'	
	AND		DD.LotNo NOT IN (SELECT LotNo FROM MISTO..ToLoss WHERE DeliveryNo = DD.DeliveryNo AND LotNo = DD.LotNo AND Seq = DD.Seq AND CancelYN <> 'Y')
	-- 20230120 고종수 이동, 교환의 이력이 있더라도 최종적으로 실반납이 된 경우
	AND		NOT EXISTS (SELECT	1
						FROM	TOMove	X
						LEFT	JOIN    TORecovery Y
						ON		X.DeliveryNo = Y.DeliveryNo
						WHERE	X.DeliveryNo = DM.DeliveryNo
						AND		DD.LotNo = Y.LotNo
						AND     Y.DeliveryNo is null)				
	-- 20230120 고종수 출고번호 기준 분실 된 이력이 있더라도 실제 분실된 Lot를 제외한 나머지 Lot가 정상 반납이 된 경우
	AND		NOT EXISTS (SELECT 1
						FROM MISTO..ToLoss a 
						LEFT JOIN TORecovery b
						ON a.deliveryno = b.DeliveryNo
						AND a.LotNo = b.LotNo
						WHERE a.DeliveryNo = dm.DeliveryNo
						AND a.LotNo = DD.LotNo
						AND a.CancelYN ='N'
						AND b.DeliveryNo is null
						)	



	SELECT @iSUM = SUM(Cnt)
	FROM   #TEMP

	SELECT s.N [ORDER]
		 , CASE s.N WHEN '01' THEN '구분'
					WHEN '02' THEN '신규임대' 
					WHEN '03' THEN '소계' 
					WHEN '04' THEN '추가임대' 
					WHEN '05' THEN '소계' 
					WHEN '06' THEN '구분' 
					WHEN '07' THEN '반납'
					WHEN '08' THEN '소계'
					WHEN '09' THEN '출고량' 
					WHEN '10' THEN '신규/추가임대'
					WHEN '11' THEN '누계'
					WHEN '12' THEN '임대량증가율'
					WHEN '13' THEN '평균 증가율'
					WHEN '14' THEN '총 합계'
		   ELSE '' END GUBUN
		 , CASE WHEN s.N IN ('02', '04', '07', '10', '12', '') THEN a.SetCode
				WHEN s.N IN ('01', '06', '09') THEN '품명'
		   ELSE '' END TOOLNM
		 , CASE WHEN s.N IN ('01', '06', '09') THEN '규격' ELSE '' END SPEC
		 , CASE WHEN s.N IN ('01', '06', '09') THEN 'SET'
		   ELSE CONVERT(VARCHAR, SUM(CASE WHEN s.N IN ('02', '03') AND a.Gubn = '신규' THEN a.Cnt
										  WHEN s.N IN ('04', '05') AND a.Gubn = '추가' THEN a.Cnt
										  WHEN s.N IN ('07', '08') AND a.Gubn = '반납' THEN a.Cnt
										  WHEN s.N IN ('10', '11') AND a.Gubn IN ('신규', '추가') THEN a.Cnt
										  WHEN s.N IN ('12', '13') THEN CASE WHEN a.Gubn = '반납' THEN a.Cnt * (-1) ELSE a.Cnt END
										  WHEN s.N IN ('14')THEN a.Cnt
									 ELSE 0 END)) END [SET]
		 , CASE WHEN s.N = '01' THEN '임대율'
				WHEN s.N = '06' THEN '반납율'
				WHEN s.N = '09' THEN '출고율'
		   ELSE CONVERT(VARCHAR, ROUND(SUM(CASE WHEN s.N IN ('02', '03') AND a.Gubn = '신규' THEN a.Cnt
											    WHEN s.N IN ('04', '05') AND a.Gubn = '추가' THEN a.Cnt
											    WHEN s.N IN ('07', '08') AND a.Gubn = '반납' THEN a.Cnt
											    WHEN s.N IN ('10', '11') AND a.Gubn IN ('신규', '추가') THEN a.Cnt
											    WHEN s.N IN ('12', '13') THEN CASE WHEN a.Gubn = '반납' THEN a.Cnt * (-1) ELSE a.Cnt END
											    WHEN s.N IN ('14')THEN a.Cnt
										   ELSE 0 END) / CASE WHEN @iSUM = 0 THEN 1 ELSE @iSUM END, 2) * 100) + '%' END RENTRATE
		 , CASE WHEN s.N IN ('01', '06', '09') THEN '비고' ELSE '' END REMARK
	FROM   #TEMP a
	CROSS JOIN (SELECT * FROM MISCM.dbo.sdummy WHERE N BETWEEN '01' AND '14') s
	GROUP BY s.N
		   , CASE WHEN s.N IN ('02', '04', '07', '10', '12', '') THEN a.SetCode
			  	  WHEN s.N IN ('01', '06', '09') THEN '품명'
		     ELSE '' END
	ORDER BY 1, 3 DESC


	DROP TABLE #TEMP

END
```

<a id="misto-str-tmsta00601-s"></a>
## `MISTO.dbo.str_TMSTA00601_S`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTO/str_TMSTA00601_S.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTO/str_TMSTA00601_S.sql

```sql

/********************************************************************************
제    목 : 기간별임대현황 구분별 내역조회
프로그램 : 기간별임대현황
등 록 일 : 2017-02-02
등 록 자 : 강윤철
수정일		수정자		내용
-----------------------------------------------------------------------
20170203	강윤철		조회대상 SETCODE변경
20170203	강윤철		사용자 요청으로 반납에서는 이동건도 포함되도록 수정
20170203	강윤철		사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
20170210	강윤철		20170203요청한 반납 시 이동건 포함되지만 해당 기간내에 일어난 이동건은 제외되도록 수정요청
20170410	양장미		공구이동처리 건 중 이동 후 생성된 건은 포함되도록
20170414	강윤철		(0410건 재수정)이동이 다 빠지는 것이 맞고 일부품목의 경우만 안빠지도록 체크로직 필요
20170526	강윤철		분실처리로 계약 종료된 건은 반납카운트 하지 않도록 수정
20220726	안진주		고객 요청으로 이동이력이 있으나, 실 반납 된 건(AS, 계약종료//이 부분은 이미 적용되어있음)은 반납 카운트가 되게끔 수정
20230330	안진주		최종적으로 정리 된 조회 요청 내용 기준으로 반납 이력 확인할 수 있게 수정처리

EXEC [str_TMSTA00601_S] 'S3', '20190101', '20190102', '충전식'
EXEC [str_TMSTA00601_S] 'S2', '20170201', '20170209', '충'
EXEC [str_TMSTA00601_S] 'S3', '20170101', '20170109', ''
EXEC [str_TMSTA00601_S] 'S3', '20170101', '20170109', '유'
EXEC [str_TMSTA00601_S] 'S3', '20170101', '20170109', '충'
*********************************************************************************/
CREATE PROCEDURE [dbo].[str_TMSTA00601_S]
	@iWorkingTag	VARCHAR(2),		-- 작업구분
	@iYMD_F			VARCHAR(8),		-- 시작년월일
	@iYMD_T			VARCHAR(8),		-- 종료년월일
	@iSetDivision	VARCHAR(6) = ''	-- 세트구분
AS
BEGIN
	SET NOCOUNT ON;

	IF @iWorkingTag = 'S1' BEGIN	-- 신규
		GOTO str_TMSTA00601_S1
	END
	ELSE IF @iWorkingTag = 'S2' BEGIN	-- 추가
		--GOTO str_TMSTA00601_S2
		GOTO str_TMSTA00601_S1
	END
	ELSE IF @iWorkingTag = 'S3' BEGIN	-- 반납
		GOTO str_TMSTA00601_S3
	END
	ELSE BEGIN
		RETURN
	END

	/*----------------------------------------------------*/
	str_TMSTA00601_S1:		-- 신규
	/*----------------------------------------------------*/
	BEGIN

		SELECT MAX(T.Division) DIVISION
			 , MAX(T.SetCode) SETCODE
			 , MAX(T.SetDivision) SETDIVISION
			 , ISNULL(T.ContractNo, T.ContractNo_Master) CONTRACTNO
			 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(T.ReleaseYmd), '-') RELEASEYMD
			 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(T.ReturnYmd), '-') RETURNYMD
			 , MAX(SC.CustNm) CUSTNM
			 , MAX(SB.BusinessNm) EQUIPCUSTNM
			 , MAX(S.SiteNm) SITENM
			 , MAX(T.DeliveryNo) DELIVERYNO
			 , MAX(CASE WHEN T.ToolNm = '죠우13' THEN T.LotNo ELSE '' END)			[J013]
			 , MAX(CASE WHEN T.ToolNm = '죠우20' THEN T.LotNo ELSE '' END)			[J020]
			 , MAX(CASE WHEN T.ToolNm = '죠우25' THEN T.LotNo ELSE '' END)			[J025]
			 , MAX(CASE WHEN T.ToolNm = '죠우30' THEN T.LotNo ELSE '' END)			[J030]
			 , MAX(CASE WHEN T.ToolNm = '죠우40' THEN T.LotNo ELSE '' END)			[J040]
			 , MAX(CASE WHEN T.ToolNm = '죠우50' THEN T.LotNo ELSE '' END)			[J050]
			 , MAX(CASE WHEN T.ToolNm = '죠우60' THEN T.LotNo ELSE '' END)			[J060]
			 , MAX(CASE WHEN T.ToolNm = '죠우75' THEN T.LotNo ELSE '' END)			[J075]
			 , MAX(CASE WHEN T.ToolNm = '죠우80' THEN T.LotNo ELSE '' END)			[J080]
			 , MAX(CASE WHEN T.ToolNm = '죠우100' THEN T.LotNo ELSE '' END)		[J100]
			 , MAX(CASE WHEN T.ToolNm = '클램핑툴중형' THEN T.LotNo ELSE '' END)	[CLAMPM]
			 , MAX(CASE WHEN T.ToolNm = '클램핑툴대형' THEN T.LotNo ELSE '' END)	[CLAMPL]
			 , MAX(CASE WHEN T.ToolNm = '건중형' THEN T.LotNo ELSE '' END)			[GUNM]
			 , MAX(CASE WHEN T.ToolNm = '건대형' THEN T.LotNo ELSE '' END)			[GUNL]
			 , MAX(CASE WHEN T.ToolNm = '실린더중형' THEN T.LotNo ELSE '' END)		[CYLINDERM]
			 , MAX(CASE WHEN T.ToolNm = '실린더대형' THEN T.LotNo ELSE '' END)		[CYLINDERL]
			 , MAX(CASE WHEN T.ToolNm = '충전기' THEN T.LotNo ELSE '' END)			[CHARGER]
			 , MAX(CASE WHEN T.ToolNm = '배터리1' THEN T.LotNo ELSE '' END)		[BATTER]
			 , MAX(CASE WHEN T.ToolNm = '배터리2' THEN T.LotNo ELSE '' END)		[BATTER2]
			 , MAX(CASE WHEN T.ToolNm = '펌프' THEN T.LotNo ELSE '' END)			[PUMP]
			 , MAX(CASE WHEN T.ToolNm = '절단기' THEN T.LotNo ELSE '' END)			[CUTTER]
		FROM  (SELECT CD.*
					, CASE WHEN DM.RentGb = '01' THEN '신규임대' ELSE '추가임대' END Division
					, CM.SetCode
					, CASE WHEN CM.SetCode IN ('01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60', '90','A','D') THEN '충전식' ELSE '유압식' END SetDivision
					, CM.ReleaseYmd
					, CM.ReturnYmd
					, CM.CustCd
					, CM.EquipCustCd
					, CM.SiteCd
					, DM.DeliveryNo
					, dbo.FN_GetToolNm(CD.LotNo) ToolNm
					, CM.ContractNo ContractNo_Master
			   FROM   TOContractMaster CM
			   INNER JOIN TODeliveryMaster DM ON DM.ContractNo = CM.ContractNo
			   LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
			   WHERE  DM.DeliveryGb = '01'
			   AND    DM.ItemGrpCd = 'SP'
			   AND    DM.DeliveryYmd BETWEEN @iYMD_F AND @iYMD_T
				-- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
			   AND    CM.CustCd != 'CG001'
			   AND    CM.SetCode IN (select ClassGrpCd FROM MISTO..TOSet A where ItemGrpCd ='SP' and chargeyn IN ('Y','N'))
				-- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
				--AND		CM.SetCode IN ('A','P','M','B','Q','R')
			   --AND    ISNULL(DM.RentGb, '') = '01'
			   AND ( (@iWorkingTag = 'S1' AND ISNULL(DM.RentGb, '') = '01')
				  OR (@iWorkingTag = 'S2' AND ISNULL(DM.RentGb, '') != '01') )
			   AND    NOT EXISTS(SELECT 1
								 FROM   TOMove
								 -- 20170410 양장미 공구이동처리 건 중 이동 후 생성된 건은 포함되도록
								 -- 20170414 강윤철 (0410건 재수정)이동이 다 빠지는 것이 맞고 일부품목의 경우만 안빠지도록 체크로직 필요
								 --WHERE (DeliveryMoveNo = DM.DeliveryNo OR (DeliveryNo = DM.DeliveryNo
								 --AND    LEFT(MoveNo, 8) BETWEEN @iYMD_F AND @iYMD_T)
								 WHERE  DeliveryMoveNo = DM.DeliveryNo
								 AND    ISNULL(PartMoveFl, '') <> 'Y')
			  ) T
		INNER JOIN MISSA.dbo.SACust SC ON SC.DuzCustCd = T.CustCd
		INNER JOIN MISSA.dbo.SABusiness SB ON SB.BusinessCd = T.EquipCustCd
		INNER JOIN TOSIte S ON S.SiteCd = T.SiteCd
		WHERE  SetDivision LIKE @iSetDivision + '%'
		GROUP BY ISNULL(T.ContractNo, T.ContractNo_Master)
		ORDER BY DIVISION, SETDIVISION, SETCODE, CONTRACTNO

/*
		SELECT	MAX(DIVISION)		DIVISION
			,	MAX(SETCODE)		SETCODE
			,	MAX(SETDIVISION)	SETDIVISION
			,	ContractNo			CONTRACTNO
			,	SUBSTRING(MAX(RELEASEYMD),1,4) + '-' + SUBSTRING(MAX(RELEASEYMD),5,2) + '-' + SUBSTRING(MAX(RELEASEYMD),7,2) AS RELEASEYMD
			,	SUBSTRING(MAX(RETURNYMD),1,4) + '-' + SUBSTRING(MAX(RETURNYMD),5,2) + '-' + SUBSTRING(MAX(RETURNYMD),7,2) AS RETURNYMD
			,	MAX(SC.CustNm)		CUSTNM
			,	MAX(SB.BusinessNm)	EQUIPCUSTNM
			,	MAX(S.SiteNm)		SITENM
			,	MAX(DeliveryNo)		DELIVERYNO
			,	MAX(CASE WHEN TOOLNM = '죠우13' THEN LOTNO ELSE '' END)			[J013]
			,	MAX(CASE WHEN TOOLNM = '죠우20' THEN LOTNO ELSE '' END)			[J020]
			,	MAX(CASE WHEN TOOLNM = '죠우25' THEN LOTNO ELSE '' END)			[J025]
			,	MAX(CASE WHEN TOOLNM = '죠우30' THEN LOTNO ELSE '' END)			[J030]
			,	MAX(CASE WHEN TOOLNM = '죠우40' THEN LOTNO ELSE '' END)			[J040]
			,	MAX(CASE WHEN TOOLNM = '죠우50' THEN LOTNO ELSE '' END)			[J050]
			,	MAX(CASE WHEN TOOLNM = '죠우60' THEN LOTNO ELSE '' END)			[J060]
			,	MAX(CASE WHEN TOOLNM = '죠우75' THEN LOTNO ELSE '' END)			[J075]
			,	MAX(CASE WHEN TOOLNM = '죠우80' THEN LOTNO ELSE '' END)			[J080]
			,	MAX(CASE WHEN TOOLNM = '죠우100' THEN LOTNO ELSE '' END)		[J100]
			,	MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN LOTNO ELSE '' END)	[CLAMPM]
			,	MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN LOTNO ELSE '' END)	[CLAMPL]
			,	MAX(CASE WHEN TOOLNM = '건중형' THEN LOTNO ELSE '' END)			[GUNM]
			,	MAX(CASE WHEN TOOLNM = '건대형' THEN LOTNO ELSE '' END)			[GUNL]
			,	MAX(CASE WHEN TOOLNM = '실린더중형' THEN LOTNO ELSE '' END)		[CYLINDERM]
			,	MAX(CASE WHEN TOOLNM = '실린더대형' THEN LOTNO ELSE '' END)		[CYLINDERL]
			,	MAX(CASE WHEN TOOLNM = '충전기' THEN LOTNO ELSE '' END)			[CHARGER]
			,	MAX(CASE WHEN TOOLNM = '배터리1' THEN LOTNO ELSE '' END)		[BATTER]
			,	MAX(CASE WHEN TOOLNM = '배터리2' THEN LOTNO ELSE '' END)		[BATTER2]
			,	MAX(CASE WHEN TOOLNM = '펌프' THEN LOTNO ELSE '' END)			[PUMP]
			,	MAX(CASE WHEN TOOLNM = '절단기' THEN LOTNO ELSE '' END)			[CUTTER]
		FROM	(SELECT	CD.*
					,	'신규임대'		DIVISION
					,	CM.SetCode	SETCODE
					,	CASE WHEN CM.SetCode = 'A' THEN '충전식' ELSE '유압식' END AS SETDIVISION
					-- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
					--,	CASE WHEN CM.SetCode IN ('A','P') THEN '충전식' ELSE '유압식' END AS SETDIVISION
					,	CM.ReleaseYmd
					,	CM.ReturnYmd
					,	CM.CustCd
					,	CM.EquipCustCd
					,	CM.SiteCd
					,	DM.DeliveryNo
					,	CASE WHEN C.CLASSLNM = '죠우' AND C.SPEC = '13' THEN '죠우13'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '20' THEN '죠우20'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '25' THEN '죠우25'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '30' THEN '죠우30'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '40' THEN '죠우40'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '50' THEN '죠우50'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '60' THEN '죠우60'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '75' THEN '죠우75'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '80' THEN '죠우80'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '100' THEN '죠우100'
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '중형' THEN '클램핑툴중형'			 
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '대형' THEN '클램핑툴대형'
							 WHEN C.CLASSLNM = '대형건' THEN '건대형'
							 WHEN C.CLASSLNM = '충전건' AND C.CLASSSNM IN ('구형','신형') THEN '건중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '중형' THEN '실린더중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '대형' THEN '실린더대형'
							 WHEN C.CLASSSNM = '충전기' THEN '충전기' 
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='01' THEN '배터리1'
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='02' THEN '배터리2'
							 WHEN C.CLASSLNM = '전동펌프' THEN '펌프'
							 WHEN C.CLASSLNM = '절단기' THEN '절단기'
						ELSE '' END TOOLNM
				FROM	TOContractDetail	CD
				JOIN	TOContractMaster	CM	ON	CM.ContractNo = CD.ContractNo
				JOIN	TODeliveryMaster	DM	ON	DM.ContractNo = CM.ContractNo
				JOIN	TOCLASS	C	ON	C.CLASSCD = CD.CLASSCD
				WHERE	DM.DeliveryGb = '01'
				AND		DM.DeliveryYmd BETWEEN @iYMD_F AND @iYMD_T
				-- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
				AND		CM.CustCd != 'CG001'
				AND		CM.SetCode IN ('A','M','B')
				-- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
				--AND		CM.SetCode IN ('A','P','M','B','Q','R')
				AND		DM.RentGb = '01'
				AND		NOT EXISTS	(SELECT	1
									   FROM	TOMove	X
									  WHERE	X.DeliveryMoveNo = DM.DeliveryNo
										 OR	X.DeliveryNo = DM.DeliveryNo)
		) RESULT
		JOIN	MISSA..SACust		SC	ON	RESULT.CustCd = SC.DuzCustCd
		JOIN	MISSA..SABusiness	SB	ON	RESULT.EquipCustCd = SB.BusinessCd
		JOIN	TOSIte	S	ON	RESULT.SiteCd = S.SiteCd
		WHERE	SETDIVISION LIKE @iSetDivision + '%'
		GROUP BY RESULT.ContractNo
		ORDER BY DIVISION, SETDIVISION, SETCODE, ContractNo
*/

		RETURN
	END

	/*----------------------------------------------------*/
	str_TMSTA00601_S2:		-- 추가
	/*----------------------------------------------------*/
	BEGIN
		SELECT	MAX(DIVISION)		DIVISION
			,	MAX(SETCODE)		SETCODE
			,	MAX(SETDIVISION)	SETDIVISION
			,	ContractNo			CONTRACTNO
			,	SUBSTRING(MAX(RELEASEYMD),1,4) + '-' + SUBSTRING(MAX(RELEASEYMD),5,2) + '-' + SUBSTRING(MAX(RELEASEYMD),7,2) AS RELEASEYMD
			,	SUBSTRING(MAX(RETURNYMD),1,4) + '-' + SUBSTRING(MAX(RETURNYMD),5,2) + '-' + SUBSTRING(MAX(RETURNYMD),7,2) AS RETURNYMD
			,	MAX(SC.CustNm)		CUSTNM
			,	MAX(SB.BusinessNm)	EQUIPCUSTNM
			,	MAX(S.SiteNm)		SITENM
			,	MAX(DeliveryNo)		DELIVERYNO
			,	MAX(CASE WHEN TOOLNM = '죠우13' THEN LOTNO ELSE '' END)			[J013]
			,	MAX(CASE WHEN TOOLNM = '죠우20' THEN LOTNO ELSE '' END)			[J020]
			,	MAX(CASE WHEN TOOLNM = '죠우25' THEN LOTNO ELSE '' END)			[J025]
			,	MAX(CASE WHEN TOOLNM = '죠우30' THEN LOTNO ELSE '' END)			[J030]
			,	MAX(CASE WHEN TOOLNM = '죠우40' THEN LOTNO ELSE '' END)			[J040]
			,	MAX(CASE WHEN TOOLNM = '죠우50' THEN LOTNO ELSE '' END)			[J050]
			,	MAX(CASE WHEN TOOLNM = '죠우60' THEN LOTNO ELSE '' END)			[J060]
			,	MAX(CASE WHEN TOOLNM = '죠우75' THEN LOTNO ELSE '' END)			[J075]
			,	MAX(CASE WHEN TOOLNM = '죠우80' THEN LOTNO ELSE '' END)			[J080]
			,	MAX(CASE WHEN TOOLNM = '죠우100' THEN LOTNO ELSE '' END)		[J100]
			,	MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN LOTNO ELSE '' END)	[CLAMPM]
			,	MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN LOTNO ELSE '' END)	[CLAMPL]
			,	MAX(CASE WHEN TOOLNM = '건중형' THEN LOTNO ELSE '' END)			[GUNM]
			,	MAX(CASE WHEN TOOLNM = '건대형' THEN LOTNO ELSE '' END)			[GUNL]
			,	MAX(CASE WHEN TOOLNM = '실린더중형' THEN LOTNO ELSE '' END)		[CYLINDERM]
			,	MAX(CASE WHEN TOOLNM = '실린더대형' THEN LOTNO ELSE '' END)		[CYLINDERL]
			,	MAX(CASE WHEN TOOLNM = '충전기' THEN LOTNO ELSE '' END)			[CHARGER]
			,	MAX(CASE WHEN TOOLNM = '배터리1' THEN LOTNO ELSE '' END)		[BATTER]
			,	MAX(CASE WHEN TOOLNM = '배터리2' THEN LOTNO ELSE '' END)		[BATTER2]
			,	MAX(CASE WHEN TOOLNM = '펌프' THEN LOTNO ELSE '' END)			[PUMP]
			,	MAX(CASE WHEN TOOLNM = '절단기' THEN LOTNO ELSE '' END)			[CUTTER]
		FROM	(SELECT	CD.*
					,	'추가임대'		DIVISION
					,	CM.SetCode	SETCODE
					,	CASE WHEN CM.SetCode IN ('01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60', '90''A','D') THEN '충전식' ELSE '유압식' END AS SETDIVISION
					-- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
					--,	CASE WHEN CM.SetCode IN ('A','P') THEN '충전식' ELSE '유압식' END AS SETDIVISION
					,	CM.ReleaseYmd
					,	CM.ReturnYmd
					,	CM.CustCd
					,	CM.EquipCustCd
					,	CM.SiteCd
					,	DM.DeliveryNo
					,	CASE WHEN C.CLASSLNM = '죠우' AND C.SPEC = '13' THEN '죠우13'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '20' THEN '죠우20'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '25' THEN '죠우25'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '30' THEN '죠우30'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '40' THEN '죠우40'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '50' THEN '죠우50'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '60' THEN '죠우60'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '75' THEN '죠우75'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '80' THEN '죠우80'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '100' THEN '죠우100'
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '중형' THEN '클램핑툴중형'			 
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '대형' THEN '클램핑툴대형'
							 WHEN C.CLASSLNM = '대형건' THEN '건대형'
							 WHEN C.CLASSLNM = '충전건' AND C.CLASSSNM IN ('구형','신형') THEN '건중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '중형' THEN '실린더중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '대형' THEN '실린더대형'
							 WHEN C.CLASSSNM = '충전기' THEN '충전기' 
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='01' THEN '배터리1'
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='02' THEN '배터리2'
							 WHEN C.CLASSLNM = '전동펌프' THEN '펌프'
							 WHEN C.CLASSLNM = '절단기' THEN '절단기'
						ELSE '' END TOOLNM
				FROM	TOContractDetail	CD
				JOIN	TOContractMaster	CM	ON	CM.ContractNo = CD.ContractNo
				JOIN	TODeliveryMaster	DM	ON	DM.ContractNo = CM.ContractNo
				JOIN	TOCLASS	C	ON	C.CLASSCD = CD.CLASSCD
				WHERE	DM.DeliveryGb = '01'
				AND	    DM.ItemGrpCd = 'SP'
				AND		DM.DeliveryYmd BETWEEN @iYMD_F AND @iYMD_T
				-- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
				AND		CM.CustCd != 'CG001'
				AND		CM.SetCode IN (select ClassGrpCd FROM MISTO..TOSet A where ItemGrpCd ='SP' and chargeyn IN ('Y','N'))
				-- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
				--AND		CM.SetCode IN ('A','P','M','B','Q','R')
				AND		DM.RentGb != '01'
				AND		ISNULL(DM.RentGb,'') != ''
				AND		NOT EXISTS	(SELECT	1
									   FROM	TOMove	X
									  WHERE	X.DeliveryMoveNo = DM.DeliveryNo
										 OR	X.DeliveryNo = DM.DeliveryNo)
		) RESULT
		JOIN	MISSA..SACust		SC	ON	RESULT.CustCd = SC.DuzCustCd
		JOIN	MISSA..SABusiness	SB	ON	RESULT.EquipCustCd = SB.BusinessCd
		JOIN	TOSIte	S	ON	RESULT.SiteCd = S.SiteCd
		WHERE	SETDIVISION LIKE @iSetDivision + '%'
		GROUP BY RESULT.ContractNo
		ORDER BY DIVISION, SETDIVISION, SETCODE, ContractNo

		RETURN
	END

	/*----------------------------------------------------*/
	str_TMSTA00601_S3:		-- 반납
	/*----------------------------------------------------*/
		BEGIN

		SELECT '임대반납' DIVISION
			 , T.SetCode SETCODE
			 , T.SetDivision SETDIVISION
			 , ISNULL(T.ContractNo, T.ContractNo_Master) CONTRACTNO
			 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(T.ReleaseYmd), '-') RELEASEYMD
			 --, MISCM.dbo.fn_gfnDateTypeTrans(MAX(T.ReturnYmd), '-') RETURNYMD
			 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(T.RecoveryYmd), '-') RETURNYMD		--20230120 고종수 - 계약일자 -> 반납일자로 수정
			 , MAX(SC.CustNm) CUSTNM
			 , MAX(SB.BusinessNm) EQUIPCUSTNM
			 , MAX(S.SiteNm) SITENM
			 , MAX(T.DeliveryNo) DELIVERYNO
			 , MAX(CASE WHEN T.ToolNm = '죠우13' THEN T.LotNo ELSE '' END)			[J013]
			 , MAX(CASE WHEN T.ToolNm = '죠우20' THEN T.LotNo ELSE '' END)			[J020]
			 , MAX(CASE WHEN T.ToolNm = '죠우25' THEN T.LotNo ELSE '' END)			[J025]
			 , MAX(CASE WHEN T.ToolNm = '죠우30' THEN T.LotNo ELSE '' END)			[J030]
			 , MAX(CASE WHEN T.ToolNm = '죠우40' THEN T.LotNo ELSE '' END)			[J040]
			 , MAX(CASE WHEN T.ToolNm = '죠우50' THEN T.LotNo ELSE '' END)			[J050]
			 , MAX(CASE WHEN T.ToolNm = '죠우60' THEN T.LotNo ELSE '' END)			[J060]
			 , MAX(CASE WHEN T.ToolNm = '죠우75' THEN T.LotNo ELSE '' END)			[J075]
			 , MAX(CASE WHEN T.ToolNm = '죠우80' THEN T.LotNo ELSE '' END)			[J080]
			 , MAX(CASE WHEN T.ToolNm = '죠우100' THEN T.LotNo ELSE '' END)		[J100]
			 , MAX(CASE WHEN T.ToolNm = '클램핑툴중형' THEN T.LotNo ELSE '' END)	[CLAMPM]
			 , MAX(CASE WHEN T.ToolNm = '클램핑툴대형' THEN T.LotNo ELSE '' END)	[CLAMPL]
			 , MAX(CASE WHEN T.ToolNm = '건중형' THEN T.LotNo ELSE '' END)			[GUNM]
			 , MAX(CASE WHEN T.ToolNm = '건대형' THEN T.LotNo ELSE '' END)			[GUNL]
			 , MAX(CASE WHEN T.ToolNm = '실린더중형' THEN T.LotNo ELSE '' END)		[CYLINDERM]
			 , MAX(CASE WHEN T.ToolNm = '실린더대형' THEN T.LotNo ELSE '' END)		[CYLINDERL]
			 , MAX(CASE WHEN T.ToolNm = '충전기' THEN T.LotNo ELSE '' END)			[CHARGER]
			 , MAX(CASE WHEN T.ToolNm = '배터리1' THEN T.LotNo ELSE '' END)		[BATTER]
			 , MAX(CASE WHEN T.ToolNm = '배터리2' THEN T.LotNo ELSE '' END)		[BATTER2]
			 , MAX(CASE WHEN T.ToolNm = '펌프' THEN T.LotNo ELSE '' END)			[PUMP]
			 , MAX(CASE WHEN T.ToolNm = '절단기' THEN T.LotNo ELSE '' END)			[CUTTER]
		FROM  (SELECT --CD.*
						CM.ContractNo
						, DD.LotNo
						, CM.SetCode
						, CASE WHEN CM.SetCode IN( '01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60','90', 'A','D')THEN '충전식' ELSE '유압식' END SetDivision
						, CM.ReleaseYmd
						, CM.ReturnYmd
						, CM.CustCd
						, CM.EquipCustCd
						, CM.SiteCd
						, ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) DeliveryNo
						, dbo.FN_GetToolNm(DD.LotNo) ToolNm
						, CM.ContractNo ContractNo_Master
						, R.RecoveryYmd
					--20170424 이동 건 반납 시 최종 이동 건의 반납일자로 Count
					FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM TODeliveryMaster A LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
					INNER JOIN TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
					LEFT JOIN TODeliveryDetail DD ON DD.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) 
					--LEFT JOIN TORecovery R ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = ISNULL(DD.PreLotNo, DD.LotNo)
					INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo, RecoveryYmd FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R 
					ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = DD.LotNo		
					WHERE  CM.ContractGb = '03'
					AND    CM.ItemGrpCd = 'SP'
					--AND    CM.ReturnYmd BETWEEN @iYMD_F AND @iYMD_T
					-- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
					AND    CM.CustCd != 'CG001'
					AND    CM.SetCode IN (select ClassGrpCd FROM MISTO..TOSet A where ItemGrpCd ='SP' and chargeyn IN ('Y','N'))
					AND DD.LotNo NOT IN (SELECT LotNo FROM MISTO..ToLoss WHERE DeliveryNo = DD.DeliveryNo AND LotNo = DD.LotNo AND Seq = DD.Seq AND CancelYN <> 'Y')
					-- 20170203 강윤철 사용자 요청으로 반납에서는 이동건도 포함되도록 수정
					--AND	NOT EXISTS	(SELECT	1
					--					FROM	TOMove	X
					--					WHERE	X.DeliveryNo = DM.DeliveryNo)
					-- 20230120 고종수 이동, 교환의 이력이 있더라도 최종적으로 실반납이 된 경우
						AND	 NOT EXISTS	(SELECT	1
											FROM	TOMove	X
											LEFT	JOIN    TORecovery Y
											ON		X.DeliveryNo = Y.DeliveryNo
											WHERE	X.DeliveryNo = DM.DeliveryNo
											AND		DD.LotNo = Y.LotNo
											AND     Y.DeliveryNo is null)				
					-- 20230120 고종수 출고번호 기준 분실 된 이력이 있더라도 실제 분실된 Lot를 제외한 나머지 Lot가 정상 반납이 된 경우
						 and not EXISTS (select 1
											FROM MISTO..ToLoss a 
											left join TORecovery b
											on a.deliveryno = b.DeliveryNo
											and a.LotNo = b.LotNo
											where a.DeliveryNo = dm.DeliveryNo
											and a.LotNo = DD.LotNo
											and a.CancelYN ='N'
											and b.DeliveryNo is null
											)

			   UNION
			   
			   SELECT --CD.*
						CM.ContractNo
						, DD.LotNo
						, CM.SetCode
					   , CASE WHEN CM.SetCode IN ( '01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60','90', 'A','D')THEN '충전식' ELSE '유압식' END SetDivision
					   , CM.ReleaseYmd
					   , CM.ReturnYmd
					   , CM.CustCd
					   , CM.EquipCustCd
					   , CM.SiteCd
					   , ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) DeliveryNo
					   , dbo.FN_GetToolNm(DD.LotNo) ToolNm
					   , CM.ContractNo ContractNo_Master
					   , R.RecoveryYmd
						FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM TODeliveryMaster A LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
						INNER JOIN TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
						--LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
						LEFT JOIN TODeliveryDetail DD ON DD.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) --AND CD.Seq = ISNULL(DD.PreSeq, DD.Seq) --AND ISNULL(DD.PreLotNo, DD.LotNo) = R.LotNo 
						--LEFT JOIN TORecovery R ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = ISNULL(DD.PreLotNo, DD.LotNo)
						INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo, RecoveryYmd FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R 
						ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = DD.LotNo		
						WHERE  CM.ContractGb = '03'
						AND    CM.ItemGrpCd = 'SP'
						--AND    CM.ReturnYmd BETWEEN @iYMD_F AND @iYMD_T

						AND    CM.CustCd != 'CG001'
						AND    CM.SetCode IN (select ClassGrpCd FROM MISTO..TOSet A where ItemGrpCd ='SP' and chargeyn IN ('Y','N'))	
						AND DD.LotNo NOT IN (SELECT LotNo FROM MISTO..ToLoss WHERE DeliveryNo = DD.DeliveryNo AND LotNo = DD.LotNo AND Seq = DD.Seq AND CancelYN <> 'Y')
						AND EXISTS (SELECT 1 FROM TOAs TS WHERE TS.DeliveryNo = DM.DeliveryNo)

			   UNION ALL
			   SELECT --CD.*
					CM.ContractNo
					, DD.LotNo
					, CM.SetCode
					, '충전식' SetDivision
					, CM.ReleaseYmd
					, CM.ReturnYmd
					, CM.CustCd
					, CM.EquipCustCd
					, CM.SiteCd
					--, DM.DeliveryNo
					, ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) DeliveryNo
					, dbo.FN_GetToolNm(DD.LotNo) ToolNm
					, CM.ContractNo ContractNo_Master	
					, R.RecoveryYmd
			   FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM TODeliveryMaster A LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
			   INNER JOIN TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
			   --LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
			   LEFT JOIN TODeliveryDetail DD ON DD.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) --AND CD.Seq = ISNULL(DD.PreSeq, DD.Seq) --AND ISNULL(DD.PreLotNo, DD.LotNo) = R.LotNo 
			   --LEFT JOIN TORecovery TR ON TR.DeliveryNo = DD.DeliveryNo AND TR.LotNo = ISNULL(DD.PreLotNo, DD.LotNo)
			   --INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = ISNULL(DD.PreLotNo, DD.LotNo)--ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo)
				INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo, RecoveryYmd FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R 
						ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = DD.LotNo		
			   WHERE  ISNULL(CM.ChargerTypeFL, '') = 'Y'
			   AND    CM.ItemGrpCd = 'SP'
			   -- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
			   AND    CM.CustCd != 'CG001'
		--	   AND    CM.ContractYmd < '20160401'			   
				AND DD.LotNo NOT IN (SELECT LotNo FROM MISTO..ToLoss WHERE DeliveryNo = DD.DeliveryNo AND LotNo = DD.LotNo AND Seq = DD.Seq AND CancelYN <> 'Y')
			   ---- 20170203 강윤철 사용자 요청으로 반납에서는 이동건도 포함되도록 수정
			   --AND	NOT EXISTS	(SELECT	1
						--		   FROM	TOMove	X
						--		  WHERE	X.DeliveryNo = DM.DeliveryNo)			   
				-- 20230120 고종수 이동, 교환의 이력이 있더라도 최종적으로 실반납이 된 경우
					AND	 NOT EXISTS	(SELECT	1
										FROM	TOMove	X
										LEFT	JOIN    TORecovery Y
										ON		X.DeliveryNo = Y.DeliveryNo
										WHERE	X.DeliveryNo = DM.DeliveryNo
										AND		DD.LotNo = Y.LotNo
										AND     Y.DeliveryNo is null)				
					-- 20230120 고종수 출고번호 기준 분실 된 이력이 있더라도 실제 분실된 Lot를 제외한 나머지 Lot가 정상 반납이 된 경우
					 and not EXISTS (select 1
										FROM MISTO..ToLoss a 
										left join TORecovery b
										on a.deliveryno = b.DeliveryNo
										and a.LotNo = b.LotNo
										where a.DeliveryNo = dm.DeliveryNo
										and a.LotNo = DD.LotNo
										and a.CancelYN ='N'
										and b.DeliveryNo is null
										)			 
			   UNION ALL
			   SELECT --CD.*
					CM.ContractNo
					, DD.LotNo
					, CM.SetCode
					, '유압식' SetDivision
					, CM.ReleaseYmd
					, CM.ReturnYmd
					, CM.CustCd
					, CM.EquipCustCd
					, CM.SiteCd					
					, ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) DeliveryNo
					, dbo.FN_GetToolNm(DD.LotNo) ToolNm
					, CM.ContractNo ContractNo_Master	
					, R.RecoveryYmd
			   --20170424 이동 건 반납 시 최종 이동 건의 반납일자로 Count
			   FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM TODeliveryMaster A LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
			   INNER JOIN TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
			   --LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
			   LEFT JOIN TODeliveryDetail DD ON DD.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) --AND CD.Seq = ISNULL(DD.PreSeq, DD.Seq) --AND ISNULL(DD.PreLotNo, DD.LotNo) = R.LotNo 
			   --LEFT JOIN TORecovery TR ON TR.DeliveryNo = DD.DeliveryNo AND TR.LotNo = ISNULL(DD.PreLotNo, DD.LotNo)
			   --INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = ISNULL(DD.PreLotNo, DD.LotNo)--ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo)
				INNER JOIN (SELECT DISTINCT DeliveryNo, LotNo, RecoveryYmd FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R 
						ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = DD.LotNo		
			   WHERE  ISNULL(CM.OilTypeFL, '') = 'Y'
			   AND    CM.ItemGrpCd = 'SP'
			   -- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
			   AND    CM.CustCd != 'CG001'	
			   --AND	NOT EXISTS	(SELECT	1
						--		   FROM	TOMove	X
						--		  WHERE	X.DeliveryNo = DM.DeliveryNo)
				AND DD.LotNo NOT IN (SELECT LotNo FROM MISTO..ToLoss WHERE DeliveryNo = DD.DeliveryNo AND LotNo = DD.LotNo AND Seq = DD.Seq AND CancelYN <> 'Y')
					-- 20230120 고종수 이동, 교환의 이력이 있더라도 최종적으로 실반납이 된 경우
					AND	 NOT EXISTS	(SELECT	1
										FROM	TOMove	X
										LEFT	JOIN    TORecovery Y
										ON		X.DeliveryNo = Y.DeliveryNo
										WHERE	X.DeliveryNo = DM.DeliveryNo
										AND		DD.LotNo = Y.LotNo
										AND     Y.DeliveryNo is null)				
					-- 20230120 고종수 출고번호 기준 분실 된 이력이 있더라도 실제 분실된 Lot를 제외한 나머지 Lot가 정상 반납이 된 경우
					 and not EXISTS (select 1
										FROM MISTO..ToLoss a 
										left join TORecovery b
										on a.deliveryno = b.DeliveryNo
										and a.LotNo = b.LotNo
										where a.DeliveryNo = dm.DeliveryNo
										and a.LotNo = DD.LotNo
										and a.CancelYN ='N'
										and b.DeliveryNo is null
										)
			  ) T
		INNER JOIN MISSA.dbo.SACust SC ON T.CustCd = SC.DuzCustCd
		INNER JOIN MISSA.dbo.SABusiness SB ON T.EquipCustCd = SB.BusinessCd
		INNER JOIN TOSIte S ON T.SiteCd = S.SiteCd
		WHERE  SetDivision LIKE @iSetDivision + '%'
		GROUP BY T.SetCode, T.SetDivision, ISNULL(T.ContractNo, T.ContractNo_Master)
		ORDER BY DIVISION, SETDIVISION, SETCODE, CONTRACTNO
		RETURN
	END
	--BEGIN

	--	SELECT '임대반납' DIVISION
	--		 , T.SetCode SETCODE
	--		 , T.SetDivision SETDIVISION
	--		 , ISNULL(T.ContractNo, T.ContractNo_Master) CONTRACTNO
	--		 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(T.ReleaseYmd), '-') RELEASEYMD
	--		 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(T.ReturnYmd), '-') RETURNYMD
	--		 , MAX(SC.CustNm) CUSTNM
	--		 , MAX(SB.BusinessNm) EQUIPCUSTNM
	--		 , MAX(S.SiteNm) SITENM
	--		 , MAX(T.DeliveryNo) DELIVERYNO
	--		 , MAX(CASE WHEN T.ToolNm = '죠우13' THEN T.LotNo ELSE '' END)			[J013]
	--		 , MAX(CASE WHEN T.ToolNm = '죠우20' THEN T.LotNo ELSE '' END)			[J020]
	--		 , MAX(CASE WHEN T.ToolNm = '죠우25' THEN T.LotNo ELSE '' END)			[J025]
	--		 , MAX(CASE WHEN T.ToolNm = '죠우30' THEN T.LotNo ELSE '' END)			[J030]
	--		 , MAX(CASE WHEN T.ToolNm = '죠우40' THEN T.LotNo ELSE '' END)			[J040]
	--		 , MAX(CASE WHEN T.ToolNm = '죠우50' THEN T.LotNo ELSE '' END)			[J050]
	--		 , MAX(CASE WHEN T.ToolNm = '죠우60' THEN T.LotNo ELSE '' END)			[J060]
	--		 , MAX(CASE WHEN T.ToolNm = '죠우75' THEN T.LotNo ELSE '' END)			[J075]
	--		 , MAX(CASE WHEN T.ToolNm = '죠우80' THEN T.LotNo ELSE '' END)			[J080]
	--		 , MAX(CASE WHEN T.ToolNm = '죠우100' THEN T.LotNo ELSE '' END)		[J100]
	--		 , MAX(CASE WHEN T.ToolNm = '클램핑툴중형' THEN T.LotNo ELSE '' END)	[CLAMPM]
	--		 , MAX(CASE WHEN T.ToolNm = '클램핑툴대형' THEN T.LotNo ELSE '' END)	[CLAMPL]
	--		 , MAX(CASE WHEN T.ToolNm = '건중형' THEN T.LotNo ELSE '' END)			[GUNM]
	--		 , MAX(CASE WHEN T.ToolNm = '건대형' THEN T.LotNo ELSE '' END)			[GUNL]
	--		 , MAX(CASE WHEN T.ToolNm = '실린더중형' THEN T.LotNo ELSE '' END)		[CYLINDERM]
	--		 , MAX(CASE WHEN T.ToolNm = '실린더대형' THEN T.LotNo ELSE '' END)		[CYLINDERL]
	--		 , MAX(CASE WHEN T.ToolNm = '충전기' THEN T.LotNo ELSE '' END)			[CHARGER]
	--		 , MAX(CASE WHEN T.ToolNm = '배터리1' THEN T.LotNo ELSE '' END)		[BATTER]
	--		 , MAX(CASE WHEN T.ToolNm = '배터리2' THEN T.LotNo ELSE '' END)		[BATTER2]
	--		 , MAX(CASE WHEN T.ToolNm = '펌프' THEN T.LotNo ELSE '' END)			[PUMP]
	--		 , MAX(CASE WHEN T.ToolNm = '절단기' THEN T.LotNo ELSE '' END)			[CUTTER]
	--	FROM  (SELECT CD.*
	--				, CM.SetCode
	--				, CASE WHEN CM.SetCode IN( '01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60','90', 'A','D')THEN '충전식' ELSE '유압식' END SetDivision
	--				, CM.ReleaseYmd
	--				, CM.ReturnYmd
	--				, CM.CustCd
	--				, CM.EquipCustCd
	--				, CM.SiteCd
	--				--, DM.DeliveryNo
	--				, ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) DeliveryNo
	--				, dbo.FN_GetToolNm(CD.LotNo) ToolNm
	--				, CM.ContractNo ContractNo_Master
	--		   --FROM   TOContractMaster CM
	--		   --LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
	--		   --INNER JOIN TODeliveryMaster DM ON DM.ContractNo = CM.ContractNo
	--	       --20170424 이동 건 반납 시 최종 이동 건의 반납일자로 Count
	--		   FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM TODeliveryMaster A LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
	--		   INNER JOIN TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
	--		   LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
	--		   WHERE  CM.ContractGb = '03'
	--		   AND    CM.ItemGrpCd = 'SP'
	--		   AND    CM.ReturnYmd BETWEEN @iYMD_F AND @iYMD_T
	--		   -- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
	--		   AND    CM.CustCd != 'CG001'
	--		   AND    CM.SetCode IN (select ClassGrpCd FROM MISTO..TOSet A where ItemGrpCd ='SP' and chargeyn IN ('Y','N'))
	--		   -- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
	--		   --AND		CM.SetCode IN ('A','P','M','B','Q','R')
	--	--	   AND    CM.ContractYmd >= '20160401'
	--		   -- 20170526	강윤철	분실처리로 계약 종료된 건은 반납카운트 하지 않도록 수정
	--		   AND		NOT EXISTS (SELECT 1
	--				FROM	ToLoss	Z
	--				WHERE	 Z.DeliveryNo = DM.DeliveryNo)
	--		   -- 20170203 강윤철 사용자 요청으로 반납에서는 이동건도 포함되도록 수정
	--		   AND	NOT EXISTS	(SELECT	1
	--							   FROM	TOMove	X
	--							  WHERE	X.DeliveryMoveNo = DM.DeliveryNo
	--							 OR	X.DeliveryNo = DM.DeliveryNo)
	--		   -- 20170210 강윤철 20170203요청한 반납 시 이동건 포함되지만 해당 기간내에 일어난 이동건은 제외되도록 수정요청
	--	--	   AND    NOT EXISTS (SELECT 1
	--		--				      FROM   TOMove
	--							  -- 20170410 양장미 공구이동처리 건 중 이동 후 생성된 건은 포함되도록
	--							  -- 20170414 강윤철 (0410건 재수정)이동이 다 빠지는 것이 맞고 일부품목의 경우만 안빠지도록 체크로직 필요
	--							  --WHERE (DeliveryMoveNo = DM.DeliveryNo OR DeliveryNo = DM.DeliveryNo)
	--						      --AND    LEFT(MoveNo, 8) BETWEEN @iYMD_F AND @iYMD_T)
	--			--				  WHERE  DeliveryMoveNo = DM.DeliveryMoveNo
	--			--				    AND    ISNULL(PartMoveFl, '') <> 'Y'
	--			--				 )

	--		   UNION
			   
	--		   SELECT CD.*
	--		   , CM.SetCode
	--		   , CASE WHEN CM.SetCode IN ( '01','02','03','06','07','08','09','10','11','14','15','16','42','43','44','45','46','47','48','49','50','51','55','60','90', 'A','D')THEN '충전식' ELSE '유압식' END SetDivision
	--		   , CM.ReleaseYmd
 --              , CM.ReturnYmd
 --              , CM.CustCd
 --              , CM.EquipCustCd
 --              , CM.SiteCd

 --              , ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) DeliveryNo
 --              , dbo.FN_GetToolNm(CD.LotNo) ToolNm
 --              , CM.ContractNo ContractNo_Master

	--			FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM TODeliveryMaster A LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
	--			INNER JOIN TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
	--			LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
	--			WHERE  CM.ContractGb = '03'
	--			AND    CM.ItemGrpCd = 'SP'
	--			AND    CM.ReturnYmd BETWEEN @iYMD_F AND @iYMD_T

	--			AND    CM.CustCd != 'CG001'
	--			AND    CM.SetCode IN (select ClassGrpCd FROM MISTO..TOSet A where ItemGrpCd ='SP' and chargeyn IN ('Y','N'))

	--			AND    NOT EXISTS (SELECT 1
	--								FROM   ToLoss   Z
	--								WHERE    Z.DeliveryNo = DM.DeliveryNo)
	--			AND EXISTS (SELECT 1 FROM TOAs TS WHERE TS.DeliveryNo = DM.DeliveryNo)

	--		   UNION ALL
	--		   SELECT CD.*
	--				, CM.SetCode
	--				, '충전식' SetDivision
	--				, CM.ReleaseYmd
	--				, CM.ReturnYmd
	--				, CM.CustCd
	--				, CM.EquipCustCd
	--				, CM.SiteCd
	--				--, DM.DeliveryNo
	--				, ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) DeliveryNo
	--				, dbo.FN_GetToolNm(CD.LotNo) ToolNm
	--				, CM.ContractNo ContractNo_Master
	--		   --FROM   TOContractMaster CM
	--		   --LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
	--		   --INNER JOIN TODeliveryMaster DM ON DM.ContractNo = CM.ContractNo
	--		   --INNER JOIN (SELECT DISTINCT DeliveryNo FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R ON R.DeliveryNo = DM.DeliveryNo
	--		   --20170424 이동 건 반납 시 최종 이동 건의 반납일자로 Count
	--		   FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM TODeliveryMaster A LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
	--		   INNER JOIN TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
	--		   LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
	--		   INNER JOIN (SELECT DISTINCT DeliveryNo FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R ON R.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo)
	--		   WHERE  ISNULL(CM.ChargerTypeFL, '') = 'Y'
	--		   AND    CM.ItemGrpCd = 'SP'
	--		   -- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
	--		   AND    CM.CustCd != 'CG001'
	--	--	   AND    CM.ContractYmd < '20160401'
	--		   -- 20170526	강윤철	분실처리로 계약 종료된 건은 반납카운트 하지 않도록 수정
	--		   AND		NOT EXISTS (SELECT 1
	--				FROM	ToLoss	Z
	--				WHERE	 Z.DeliveryNo = DM.DeliveryNo)
	--		   -- 20170203 강윤철 사용자 요청으로 반납에서는 이동건도 포함되도록 수정
	--		   --AND	NOT EXISTS	(SELECT	1
	--					--		   FROM	TOMove	X
	--					--		  WHERE	X.DeliveryMoveNo = DM.DeliveryNo
	--					--			 OR	X.DeliveryNo = DM.DeliveryNo)
	--		   -- 20170210 강윤철 20170203요청한 반납 시 이동건 포함되지만 해당 기간내에 일어난 이동건은 제외되도록 수정요청
	--		   AND    NOT EXISTS (SELECT 1
	--						      FROM   TOMove
	--							  -- 20170410 양장미 공구이동처리 건 중 이동 후 생성된 건은 포함되도록
	--							  -- 20170414 강윤철 (0410건 재수정)이동이 다 빠지는 것이 맞고 일부품목의 경우만 안빠지도록 체크로직 필요
	--							  --WHERE (DeliveryMoveNo = DM.DeliveryNo OR DeliveryNo = DM.DeliveryNo)
	--						      --AND    LEFT(MoveNo, 8) BETWEEN @iYMD_F AND @iYMD_T)
	--							  WHERE  DeliveryMoveNo = DM.DeliveryMoveNo
	--							    AND    ISNULL(PartMoveFl, '') <> 'Y'
	--							  )
	--		   UNION ALL
	--		   SELECT CD.*
	--				, CM.SetCode
	--				, '유압식' SetDivision
	--				, CM.ReleaseYmd
	--				, CM.ReturnYmd
	--				, CM.CustCd
	--				, CM.EquipCustCd
	--				, CM.SiteCd
	--				--, DM.DeliveryNo
	--				, ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo) DeliveryNo
	--				, dbo.FN_GetToolNm(CD.LotNo) ToolNm
	--				, CM.ContractNo ContractNo_Master
	--		   --FROM   TOContractMaster CM
	--		   --LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
	--		   --INNER JOIN TODeliveryMaster DM ON DM.ContractNo = CM.ContractNo
	--		   --INNER JOIN (SELECT DISTINCT DeliveryNo FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R ON R.DeliveryNo = DM.DeliveryNo
	--		   --20170424 이동 건 반납 시 최종 이동 건의 반납일자로 Count
	--		   FROM   (SELECT A.*, B.DeliveryNo DeliveryMoveNo, B.ContractNo ContractMoveNo FROM TODeliveryMaster A LEFT JOIN TODeliveryMaster B ON B.DeliveryNo = dbo.FN_GetLastMoveNo(A.DeliveryNo)) DM
	--		   INNER JOIN TOContractMaster CM ON CM.ContractNo = ISNULL(DM.ContractMoveNo, DM.ContractNo)
	--		   LEFT JOIN TOContractDetail CD ON CD.ContractNo = CM.ContractNo
	--		   INNER JOIN (SELECT DISTINCT DeliveryNo FROM TORecovery WHERE RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T) R ON R.DeliveryNo = ISNULL(DM.DeliveryMoveNo, DM.DeliveryNo)
	--		   WHERE  ISNULL(CM.OilTypeFL, '') = 'Y'
	--		   AND    CM.ItemGrpCd = 'SP'
	--		   -- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
	--		   AND    CM.CustCd != 'CG001'
	----		   AND    CM.ContractYmd < '20160401'
	--		   -- 20170526	강윤철	분실처리로 계약 종료.된 건은 반납카운트 하지 않도록 수정
	--		   AND		NOT EXISTS (SELECT 1
	--				FROM	ToLoss	Z
	--				WHERE	 Z.DeliveryNo = DM.DeliveryNo)
	--		   -- 20170203 강윤철 사용자 요청으로 반납에서는 이동건도 포함되도록 수정
	--		   --AND	NOT EXISTS	(SELECT	1
	--					--		   FROM	TOMove	X
	--					--		  WHERE	X.DeliveryMoveNo = DM.DeliveryNo
	--					--			 OR	X.DeliveryNo = DM.DeliveryNo)
	--		   -- 20170210 강윤철 20170203요청한 반납 시 이동건 포함되지만 해당 기간내에 일어난 이동건은 제외되도록 수정요청
	--		   AND    NOT EXISTS (SELECT 1
	--						      FROM   TOMove
	--							  -- 20170410 양장미 공구이동처리 건 중 이동 후 생성된 건은 포함되도록
	--							  -- 20170414 강윤철 (0410건 재수정)이동이 다 빠지는 것이 맞고 일부품목의 경우만 안빠지도록 체크로직 필요
	--							  --WHERE (DeliveryMoveNo = DM.DeliveryNo OR DeliveryNo = DM.DeliveryNo)
	--						      --AND    LEFT(MoveNo, 8) BETWEEN @iYMD_F AND @iYMD_T)
	--							  WHERE  DeliveryMoveNo = DM.DeliveryMoveNo
	--							    AND    ISNULL(PartMoveFl, '') <> 'Y'
	--							  )
	--		  ) T
	--	INNER JOIN MISSA.dbo.SACust SC ON T.CustCd = SC.DuzCustCd
	--	INNER JOIN MISSA.dbo.SABusiness SB ON T.EquipCustCd = SB.BusinessCd
	--	INNER JOIN TOSIte S ON T.SiteCd = S.SiteCd
	--	WHERE  SetDivision LIKE @iSetDivision + '%'
	--	GROUP BY T.SetCode, T.SetDivision, ISNULL(T.ContractNo, T.ContractNo_Master)
	--	ORDER BY DIVISION, SETDIVISION, SETCODE, CONTRACTNO

/*
		SELECT	DIVISION			DIVISION
			,	SETCODE				SETCODE
			,	SETDIVISION			SETDIVISION
			,	ContractNo			CONTRACTNO
			,	SUBSTRING(MAX(RELEASEYMD),1,4) + '-' + SUBSTRING(MAX(RELEASEYMD),5,2) + '-' + SUBSTRING(MAX(RELEASEYMD),7,2) AS RELEASEYMD
			,	SUBSTRING(MAX(RETURNYMD),1,4) + '-' + SUBSTRING(MAX(RETURNYMD),5,2) + '-' + SUBSTRING(MAX(RETURNYMD),7,2) AS RETURNYMD
			,	MAX(SC.CustNm)		CUSTNM
			,	MAX(SB.BusinessNm)	EQUIPCUSTNM
			,	MAX(S.SiteNm)		SITENM
			,	MAX(DeliveryNo)		DELIVERYNO
			,	MAX(CASE WHEN TOOLNM = '죠우13' THEN LOTNO ELSE '' END)			[J013]
			,	MAX(CASE WHEN TOOLNM = '죠우20' THEN LOTNO ELSE '' END)			[J020]
			,	MAX(CASE WHEN TOOLNM = '죠우25' THEN LOTNO ELSE '' END)			[J025]
			,	MAX(CASE WHEN TOOLNM = '죠우30' THEN LOTNO ELSE '' END)			[J030]
			,	MAX(CASE WHEN TOOLNM = '죠우40' THEN LOTNO ELSE '' END)			[J040]
			,	MAX(CASE WHEN TOOLNM = '죠우50' THEN LOTNO ELSE '' END)			[J050]
			,	MAX(CASE WHEN TOOLNM = '죠우60' THEN LOTNO ELSE '' END)			[J060]
			,	MAX(CASE WHEN TOOLNM = '죠우75' THEN LOTNO ELSE '' END)			[J075]
			,	MAX(CASE WHEN TOOLNM = '죠우80' THEN LOTNO ELSE '' END)			[J080]
			,	MAX(CASE WHEN TOOLNM = '죠우100' THEN LOTNO ELSE '' END)		[J100]
			,	MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN LOTNO ELSE '' END)	[CLAMPM]
			,	MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN LOTNO ELSE '' END)	[CLAMPL]
			,	MAX(CASE WHEN TOOLNM = '건중형' THEN LOTNO ELSE '' END)			[GUNM]
			,	MAX(CASE WHEN TOOLNM = '건대형' THEN LOTNO ELSE '' END)			[GUNL]
			,	MAX(CASE WHEN TOOLNM = '실린더중형' THEN LOTNO ELSE '' END)		[CYLINDERM]
			,	MAX(CASE WHEN TOOLNM = '실린더대형' THEN LOTNO ELSE '' END)		[CYLINDERL]
			,	MAX(CASE WHEN TOOLNM = '충전기' THEN LOTNO ELSE '' END)			[CHARGER]
			,	MAX(CASE WHEN TOOLNM = '배터리1' THEN LOTNO ELSE '' END)		[BATTER]
			,	MAX(CASE WHEN TOOLNM = '배터리2' THEN LOTNO ELSE '' END)		[BATTER2]
			,	MAX(CASE WHEN TOOLNM = '펌프' THEN LOTNO ELSE '' END)			[PUMP]
			,	MAX(CASE WHEN TOOLNM = '절단기' THEN LOTNO ELSE '' END)			[CUTTER]
		FROM	(SELECT	CD.*
					,	'임대반납'		DIVISION
					,	CM.SetCode	SETCODE
					,	CASE WHEN CM.SetCode = 'A' THEN '충전식' ELSE '유압식' END AS SETDIVISION
					-- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
					--,	CASE WHEN CM.SetCode IN ('A','P') THEN '충전식' ELSE '유압식' END AS SETDIVISION
					,	CM.ReleaseYmd
					,	CM.ReturnYmd
					,	CM.CustCd
					,	CM.EquipCustCd
					,	CM.SiteCd
					,	DM.DeliveryNo
					,	CASE WHEN C.CLASSLNM = '죠우' AND C.SPEC = '13' THEN '죠우13'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '20' THEN '죠우20'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '25' THEN '죠우25'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '30' THEN '죠우30'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '40' THEN '죠우40'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '50' THEN '죠우50'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '60' THEN '죠우60'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '75' THEN '죠우75'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '80' THEN '죠우80'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '100' THEN '죠우100'
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '중형' THEN '클램핑툴중형'			 
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '대형' THEN '클램핑툴대형'
							 WHEN C.CLASSLNM = '대형건' THEN '건대형'
							 WHEN C.CLASSLNM = '충전건' AND C.CLASSSNM IN ('구형','신형') THEN '건중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '중형' THEN '실린더중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '대형' THEN '실린더대형'
							 WHEN C.CLASSSNM = '충전기' THEN '충전기' 
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='01' THEN '배터리1'
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='02' THEN '배터리2'
							 WHEN C.CLASSLNM = '전동펌프' THEN '펌프'
							 WHEN C.CLASSLNM = '절단기' THEN '절단기'
						ELSE '' END TOOLNM
				FROM	TOContractDetail	CD
				JOIN	TOContractMaster	CM	ON	CM.ContractNo = CD.ContractNo
				JOIN	TODeliveryMaster	DM	ON	DM.ContractNo = CM.ContractNo
				JOIN	TOCLASS	C	ON	C.CLASSCD = CD.CLASSCD
				WHERE	CM.ContractGb = '03'
				AND		CM.ReturnYmd BETWEEN @iYMD_F AND @iYMD_T
				-- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
				AND		CM.CustCd != 'CG001'
				AND		CM.SetCode IN ('A','M','B')
				-- 20170203 강윤철 사용자 요청으로 대상 SETCODE변경
				--AND		CM.SetCode IN ('A','P','M','B','Q','R')
				AND		CM.ContractYmd >= '20160401'
				-- 20170203 강윤철 사용자 요청으로 반납에서는 이동건도 포함되도록 수정
				--AND	NOT EXISTS	(SELECT	1
							--		   FROM	TOMove	X
							--		  WHERE	X.DeliveryMoveNo = DM.DeliveryNo
							--			 OR	X.DeliveryNo = DM.DeliveryNo)
				-- 20170210 강윤철 20170203요청한 반납 시 이동건 포함되지만 해당 기간내에 일어난 이동건은 제외되도록 수정요청
				AND	NOT EXISTS	(SELECT	1
								   FROM	TOMove	X
								  WHERE	(X.DeliveryMoveNo = DM.DeliveryNo OR X.DeliveryNo = DM.DeliveryNo)
								    AND	SUBSTRING(X.MoveNo, 1, 8) BETWEEN @iYMD_F AND @iYMD_T)

				UNION ALL

				SELECT	CD.*
					,	'임대반납'		DIVISION
					,	CM.SetCode		SETCODE
					,	'충전식'		SETDIVISION
					,	CM.ReleaseYmd
					,	CM.ReturnYmd
					,	CM.CustCd
					,	CM.EquipCustCd
					,	CM.SiteCd
					,	DM.DeliveryNo
					,	CASE WHEN C.CLASSLNM = '죠우' AND C.SPEC = '13' THEN '죠우13'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '20' THEN '죠우20'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '25' THEN '죠우25'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '30' THEN '죠우30'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '40' THEN '죠우40'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '50' THEN '죠우50'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '60' THEN '죠우60'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '75' THEN '죠우75'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '80' THEN '죠우80'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '100' THEN '죠우100'
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '중형' THEN '클램핑툴중형'			 
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '대형' THEN '클램핑툴대형'
							 WHEN C.CLASSLNM = '대형건' THEN '건대형'
							 WHEN C.CLASSLNM = '충전건' AND C.CLASSSNM IN ('구형','신형') THEN '건중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '중형' THEN '실린더중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '대형' THEN '실린더대형'
							 WHEN C.CLASSSNM = '충전기' THEN '충전기' 
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='01' THEN '배터리1'
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='02' THEN '배터리2'
							 WHEN C.CLASSLNM = '전동펌프' THEN '펌프'
							 WHEN C.CLASSLNM = '절단기' THEN '절단기'
						ELSE '' END TOOLNM
				FROM	TOContractDetail	CD
				JOIN	TOContractMaster	CM	ON	CM.ContractNo = CD.ContractNo
				JOIN	TODeliveryMaster	DM	ON	DM.ContractNo = CM.ContractNo
				JOIN	TORecovery			R	ON	R.DeliveryNo = DM.DeliveryNo AND ISNULL(R.RecoveryYmd,'') != ''
				JOIN	TOCLASS				C	ON	C.CLASSCD = CD.CLASSCD
				WHERE	R.RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T
				-- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
				AND		CM.CustCd != 'CG001'
				AND		ISNULL(CM.ChargerTypeFL,'N') = 'Y'
				AND		CM.ContractYmd < '20160401'
				-- 20170203 강윤철 사용자 요청으로 반납에서는 이동건도 포함되도록 수정
				--AND	NOT EXISTS	(SELECT	1
						--		   FROM	TOMove	X
						--		  WHERE	X.DeliveryMoveNo = DM.DeliveryNo
						--			 OR	X.DeliveryNo = DM.DeliveryNo)
				-- 20170210 강윤철 20170203요청한 반납 시 이동건 포함되지만 해당 기간내에 일어난 이동건은 제외되도록 수정요청
				AND	NOT EXISTS	(SELECT	1
								   FROM	TOMove	X
								  WHERE	(X.DeliveryMoveNo = DM.DeliveryNo OR X.DeliveryNo = DM.DeliveryNo)
								    AND	SUBSTRING(X.MoveNo, 1, 8) BETWEEN @iYMD_F AND @iYMD_T)

				UNION ALL

				SELECT	CD.*
					,	'임대반납'		DIVISION
					,	CM.SetCode		SETCODE
					,	'유압식'		SETDIVISION
					,	CM.ReleaseYmd
					,	CM.ReturnYmd
					,	CM.CustCd
					,	CM.EquipCustCd
					,	CM.SiteCd
					,	DM.DeliveryNo
					,	CASE WHEN C.CLASSLNM = '죠우' AND C.SPEC = '13' THEN '죠우13'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '20' THEN '죠우20'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '25' THEN '죠우25'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '30' THEN '죠우30'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '40' THEN '죠우40'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '50' THEN '죠우50'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '60' THEN '죠우60'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '75' THEN '죠우75'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '80' THEN '죠우80'
							 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '100' THEN '죠우100'
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '중형' THEN '클램핑툴중형'			 
							 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '대형' THEN '클램핑툴대형'
							 WHEN C.CLASSLNM = '대형건' THEN '건대형'
							 WHEN C.CLASSLNM = '충전건' AND C.CLASSSNM IN ('구형','신형') THEN '건중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '중형' THEN '실린더중형'
							 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '대형' THEN '실린더대형'
							 WHEN C.CLASSSNM = '충전기' THEN '충전기' 
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='01' THEN '배터리1'
							 WHEN C.CLASSSNM = '배터리' AND RIGHT(CD.LotNo,2) ='02' THEN '배터리2'
							 WHEN C.CLASSLNM = '전동펌프' THEN '펌프'
							 WHEN C.CLASSLNM = '절단기' THEN '절단기'
						ELSE '' END TOOLNM
				FROM	TOContractDetail	CD
				JOIN	TOContractMaster	CM	ON	CM.ContractNo = CD.ContractNo
				JOIN	TODeliveryMaster	DM	ON	DM.ContractNo = CM.ContractNo
				JOIN	TORecovery			R	ON	R.DeliveryNo = DM.DeliveryNo AND ISNULL(R.RecoveryYmd,'') != ''
				JOIN	TOCLASS	C	ON	C.CLASSCD = CD.CLASSCD
				WHERE	R.RecoveryYmd BETWEEN @iYMD_F AND @iYMD_T
				-- 20170203 강윤철 사용자 요청으로 조회대상에서 거래처-영업팀(CG001)은 제외
				AND		CM.CustCd != 'CG001'
				AND		ISNULL(CM.OilTypeFL,'N') = 'Y'
				AND		CM.ContractYmd < '20160401'
				-- 20170203 강윤철 사용자 요청으로 반납에서는 이동건도 포함되도록 수정
				--AND	NOT EXISTS	(SELECT	1
							--		   FROM	TOMove	X
							--		  WHERE	X.DeliveryMoveNo = DM.DeliveryNo
							--			 OR	X.DeliveryNo = DM.DeliveryNo)
				-- 20170210 강윤철 20170203요청한 반납 시 이동건 포함되지만 해당 기간내에 일어난 이동건은 제외되도록 수정요청
				AND	NOT EXISTS	(SELECT	1
								   FROM	TOMove	X
								  WHERE	(X.DeliveryMoveNo = DM.DeliveryNo OR X.DeliveryNo = DM.DeliveryNo)
								    AND	SUBSTRING(X.MoveNo, 1, 8) BETWEEN @iYMD_F AND @iYMD_T)
		) RESULT
		JOIN	MISSA..SACust		SC	ON	RESULT.CustCd = SC.DuzCustCd
		JOIN	MISSA..SABusiness	SB	ON	RESULT.EquipCustCd = SB.BusinessCd
		JOIN	TOSIte	S	ON	RESULT.SiteCd = S.SiteCd
		WHERE	SETDIVISION LIKE @iSetDivision + '%'
		GROUP BY RESULT.DIVISION, RESULT.SETCODE, RESULT.SETDIVISION, RESULT.ContractNo
		ORDER BY DIVISION, SETDIVISION, SETCODE, ContractNo
*/
	--	RETURN
	--END
END
```

<a id="misto-str-tmdel00800-s"></a>
## `MISTO.dbo.str_TMDEL00800_S`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTO/str_TMDEL00800_S.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTO/str_TMDEL00800_S.sql

```sql

/********************************************************************************
제    목 : 현장관리
프로그램 : [str_TMDEL00700_S]
등 록 일 : 2015-12-15
등 록 자 : 정재광
수정일		수정자		내용
-----------------------------------------------------------------------
2016.02.24	정재광	대리점,설비업체,현장별로 한건씩만 조회되게 수정
2017.05.11	강윤철	한 건 조회 시 계약종료여부를 해당건으로 특정하면 안되고 대리점, 설비업체, 현장 전체 출고가 모두 종료되어야 종료로 표기되도록 수정
2022.12.14	안진주	준공일자(공구회수예정일) 운영기 적용작업

EXEC [str_TMDEL00700_S] @iDATEF = '20161201', @iDATET = '20171229', @iCUSTCD = 'AA020',@iEquipCustCd = '2341',@iSiteCd=''
*********************************************************************************/
CREATE PROCEDURE [dbo].[str_TMDEL00800_S]
	 @iDeliveryGb varchar(2) = '01'	-- 출고구분
	,@iCustCd varchar(5)		-- 거래처
	,@iEquipCustCd varchar(50)	-- 설비업체
	,@iSiteCD varchar(50)		-- 현장명
	,@iContractGB varchar(10) = '01'
	,@iGubun varchar(2) = '%'

AS
BEGIN
	SET NOCOUNT ON;


	SELECT DeliveryNo DELIVERYNO
		 , RowNum
		 , MAX(ContractNo) CONTRACTNO
		 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(ContractYmd), '-') CONTRACTYMD                      --계약일자
		 , CASE WHEN RowNum = 1 THEN MAX(ISNULL(ChargerTypeFL, 'N')) ELSE 'N' END CHARGERTYPEFL
		 , CASE WHEN RowNum = 1 THEN MAX(ISNULL(OilTypeFL, 'N')) ELSE 'N' END OILTYPEFL
		 , MAX(StandardFee) STANDARDFEE
		 , MAX(RebateAmt) REBATEAMT
		 , MAX(ContractFee) CONTRACTFEE
		 , CASE WHEN RowNum = 1 THEN MAX(ContractFee)  ELSE 0 END CONTRACTFEE_D
		 , CASE WHEN @iDeliveryGb = '02' OR RowNum = 1 THEN DeliveryGb ELSE '03' END DELIVERYGB
		 , CASE WHEN @iDeliveryGb = '02' OR RowNum = 1 THEN MISTW.DBO.fn_comCodeNm('TMA006', DeliveryGb, '1') ELSE MISTW.DBO.fn_comCodeNm('TMA006', '03', '1') END DELIVERYGBNM
		 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(DeliveryYmd), '-') DELIVERYYMD                       --거래일자
		 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(ReturnYmd), '-') RETURNYMD                           --종료일자
		 , CustCd CUSTCD
		 , MAX(EquipCustCd) EQUIPCUSTCD
		 , MAX(SiteCd) SITECD
		 , (SELECT CustNm FROM MISSA.dbo.SACust WHERE DuzCustCd = A.CustCd) CUSTNM
		 , (SELECT BusinessNm FROM MISSA.dbo.SABusiness WHERE BusinessCd = MAX(A.EquipCustCd)) EQUIPCUSTNM
		 , (SELECT SiteNm FROM TOSIte WHERE SiteCd = MAX(A.SiteCd)) SITENM
		 , (SELECT PlaceNm FROM TOPlace WHERE PlaceCd = MAX(A.DeliveryPlace)) DELIVERYPLACE
		 , CASE WHEN RowNum = 1 THEN MAX(Request) ELSE '' END REQUEST
		 , MAX(TelNo) TELNO
		 , MAX(DeliveryMoveNo) DELIVERYMOVENO
		 , MAX(DNO) DNO
		 , MAX(ContractGb) CONTRACTGB
		 , MAX(LotCnt) LOTCNT
		 , CASE WHEN @iDeliveryGb = '02' OR RowNum = 1 THEN  MAX(Remark) ELSE '' END AS REMARK
		-- , CASE WHEN @iWebYn='Y' THEN '' ELSE MAX(Remark) END AS REMARK
		--20221128 준공일자
		 , COMPLTDT		AS COMPLTDT
		 --선출고 있는 건의 대체회수 여부(선출고품의 대체회수 못하기로, 원품목으로 대체회수)
		 , MAX(CASE WHEN TOOLNM = '죠우13'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J013]
		 , MAX(CASE WHEN TOOLNM = '죠우20'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J020]
		 , MAX(CASE WHEN TOOLNM = '죠우25'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J025]
		 , MAX(CASE WHEN TOOLNM = '죠우30'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J030]
		 , MAX(CASE WHEN TOOLNM = '죠우40'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J040]
		 , MAX(CASE WHEN TOOLNM = '죠우50'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J050]
		 , MAX(CASE WHEN TOOLNM = '죠우60'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J060]
		 , MAX(CASE WHEN TOOLNM = '죠우75'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J075]
		 , MAX(CASE WHEN TOOLNM = '죠우80'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J080]
		 , MAX(CASE WHEN TOOLNM = '죠우100'      THEN PreExChangeFl ELSE '' END)	[EXCHANGE_J100]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN PreExChangeFl ELSE '' END)	[EXCHANGE_CLAMPM]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN PreExChangeFl ELSE '' END)	[EXCHANGE_CLAMPL]
		 , MAX(CASE WHEN TOOLNM = '건중형'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_GUNM]
		 , MAX(CASE WHEN TOOLNM = '건대형'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_GUNL]
		 , MAX(CASE WHEN TOOLNM = '실린더중형'   THEN PreExChangeFl ELSE '' END)	[EXCHANGE_CYLINDERM]
		 , MAX(CASE WHEN TOOLNM = '실린더대형'   THEN PreExChangeFl ELSE '' END)	[EXCHANGE_CYLINDERL]
		 , MAX(CASE WHEN TOOLNM = '충전기'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_CHARGER]
		 , MAX(CASE WHEN TOOLNM = '배터리1'      THEN PreExChangeFl ELSE '' END)	[EXCHANGE_BATTER]
		 , MAX(CASE WHEN TOOLNM = '배터리2'      THEN PreExChangeFl ELSE '' END)	[EXCHANGE_BATTER2]
		 , MAX(CASE WHEN TOOLNM = '펌프'         THEN PreExChangeFl ELSE '' END)	[EXCHANGE_PUMP]
		 , MAX(CASE WHEN TOOLNM = '절단기'       THEN PreExChangeFl ELSE '' END)	[EXCHANGE_CUTTER]

		 , MAX(CASE WHEN TOOLNM = '죠우13'       THEN LotNo ELSE '' END)	[J013]
		 , MAX(CASE WHEN TOOLNM = '죠우20'       THEN LotNo ELSE '' END)	[J020]
		 , MAX(CASE WHEN TOOLNM = '죠우25'       THEN LotNo ELSE '' END)	[J025]
		 , MAX(CASE WHEN TOOLNM = '죠우30'       THEN LotNo ELSE '' END)	[J030]
		 , MAX(CASE WHEN TOOLNM = '죠우40'       THEN LotNo ELSE '' END)	[J040]
		 , MAX(CASE WHEN TOOLNM = '죠우50'       THEN LotNo ELSE '' END)	[J050]
		 , MAX(CASE WHEN TOOLNM = '죠우60'       THEN LotNo ELSE '' END)	[J060]
		 , MAX(CASE WHEN TOOLNM = '죠우75'       THEN LotNo ELSE '' END)	[J075]
		 , MAX(CASE WHEN TOOLNM = '죠우80'       THEN LotNo ELSE '' END)	[J080]
		 , MAX(CASE WHEN TOOLNM = '죠우100'      THEN LotNo ELSE '' END)	[J100]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN LotNo ELSE '' END)	[CLAMPM]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN LotNo ELSE '' END)	[CLAMPL]
		 , MAX(CASE WHEN TOOLNM = '건중형'       THEN LotNo ELSE '' END)	[GUNM]
		 , MAX(CASE WHEN TOOLNM = '건대형'       THEN LotNo ELSE '' END)	[GUNL]
		 , MAX(CASE WHEN TOOLNM = '실린더중형'   THEN LotNo ELSE '' END)	[CYLINDERM]
		 , MAX(CASE WHEN TOOLNM = '실린더대형'   THEN LotNo ELSE '' END)	[CYLINDERL]
		 , MAX(CASE WHEN TOOLNM = '충전기'       THEN LotNo ELSE '' END)	[CHARGER]
		 , MAX(CASE WHEN TOOLNM = '배터리1'      THEN LotNo ELSE '' END)	[BATTER]
		 , MAX(CASE WHEN TOOLNM = '배터리2'      THEN LotNo ELSE '' END)	[BATTER2]
		 , MAX(CASE WHEN TOOLNM = '펌프'         THEN LotNo ELSE '' END)	[PUMP]
		 , MAX(CASE WHEN TOOLNM = '절단기'       THEN LotNo ELSE '' END)	[CUTTER]

		 , MAX(CASE WHEN TOOLNM = '죠우13'       THEN LotCnt ELSE 0 END)	[CNT_J013]
		 , MAX(CASE WHEN TOOLNM = '죠우20'       THEN LotCnt ELSE 0 END)	[CNT_J020]
		 , MAX(CASE WHEN TOOLNM = '죠우25'       THEN LotCnt ELSE 0 END)	[CNT_J025]
		 , MAX(CASE WHEN TOOLNM = '죠우30'       THEN LotCnt ELSE 0 END)	[CNT_J030]
		 , MAX(CASE WHEN TOOLNM = '죠우40'       THEN LotCnt ELSE 0 END)	[CNT_J040]
		 , MAX(CASE WHEN TOOLNM = '죠우50'       THEN LotCnt ELSE 0 END)	[CNT_J050]
		 , MAX(CASE WHEN TOOLNM = '죠우60'       THEN LotCnt ELSE 0 END)	[CNT_J060]
		 , MAX(CASE WHEN TOOLNM = '죠우75'       THEN LotCnt ELSE 0 END)	[CNT_J075]
		 , MAX(CASE WHEN TOOLNM = '죠우80'       THEN LotCnt ELSE 0 END)	[CNT_J080]
		 , MAX(CASE WHEN TOOLNM = '죠우100'      THEN LotCnt ELSE 0 END)	[CNT_J100]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN LotCnt ELSE 0 END)	[CNT_CLAMPM]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN LotCnt ELSE 0 END)	[CNT_CLAMPL]
		 , MAX(CASE WHEN TOOLNM = '건중형'       THEN LotCnt ELSE 0 END)	[CNT_GUNM]
		 , MAX(CASE WHEN TOOLNM = '건대형'       THEN LotCnt ELSE 0 END)	[CNT_GUNL]
		 , MAX(CASE WHEN TOOLNM = '실린더중형'   THEN LotCnt ELSE 0 END)	[CNT_CYLINDERM]
		 , MAX(CASE WHEN TOOLNM = '실린더대형'   THEN LotCnt ELSE 0 END)	[CNT_CYLINDERL]
		 , SUM(CASE WHEN TOOLNM = '충전기'       THEN LotCnt ELSE 0 END)	[CNT_CHARGER]
		 , SUM(CASE WHEN TOOLNM = '배터리1'      THEN LotCnt ELSE 0 END)	[CNT_BATTER]
		 , SUM(CASE WHEN TOOLNM = '배터리2'      THEN LotCnt ELSE 0 END)	[CNT_BATTER2]
		 , MAX(CASE WHEN TOOLNM = '펌프'         THEN LotCnt ELSE 0 END)	[CNT_PUMP]		
		 , MAX(CASE WHEN TOOLNM = '절단기'       THEN LotCnt ELSE 0 END)	[CNT_CUTTER]

		 --선출고 표시
		 , MAX(CASE WHEN TOOLNM = '죠우13'       THEN PreCnt ELSE 0 END)	[CNT2_J013]
		 , MAX(CASE WHEN TOOLNM = '죠우20'       THEN PreCnt ELSE 0 END)	[CNT2_J020]
		 , MAX(CASE WHEN TOOLNM = '죠우25'       THEN PreCnt ELSE 0 END)	[CNT2_J025]
		 , MAX(CASE WHEN TOOLNM = '죠우30'       THEN PreCnt ELSE 0 END)	[CNT2_J030]
		 , MAX(CASE WHEN TOOLNM = '죠우40'       THEN PreCnt ELSE 0 END)	[CNT2_J040]
		 , MAX(CASE WHEN TOOLNM = '죠우50'       THEN PreCnt ELSE 0 END)	[CNT2_J050]
		 , MAX(CASE WHEN TOOLNM = '죠우60'       THEN PreCnt ELSE 0 END)	[CNT2_J060]
		 , MAX(CASE WHEN TOOLNM = '죠우75'       THEN PreCnt ELSE 0 END)	[CNT2_J075]
		 , MAX(CASE WHEN TOOLNM = '죠우80'       THEN PreCnt ELSE 0 END)	[CNT2_J080]
		 , MAX(CASE WHEN TOOLNM = '죠우100'      THEN PreCnt ELSE 0 END)	[CNT2_J100]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN PreCnt ELSE 0 END)	[CNT2_CLAMPM]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN PreCnt ELSE 0 END)	[CNT2_CLAMPL]
		 , MAX(CASE WHEN TOOLNM = '건중형'       THEN PreCnt ELSE 0 END)	[CNT2_GUNM]
		 , MAX(CASE WHEN TOOLNM = '건대형'       THEN PreCnt ELSE 0 END)	[CNT2_GUNL]
		 , MAX(CASE WHEN TOOLNM = '실린더중형'   THEN PreCnt ELSE 0 END)	[CNT2_CYLINDERM]
		 , MAX(CASE WHEN TOOLNM = '실린더대형'   THEN PreCnt ELSE 0 END)	[CNT2_CYLINDERL]
		 , MAX(CASE WHEN TOOLNM = '충전기'       THEN PreCnt ELSE 0 END)	[CNT2_CHARGER]
		 , MAX(CASE WHEN TOOLNM = '배터리1'      THEN PreCnt ELSE 0 END)	[CNT2_BATTER]		
		 , MAX(CASE WHEN TOOLNM = '배터리2'      THEN PreCnt ELSE 0 END)	[CNT2_BATTER2]
		 , MAX(CASE WHEN TOOLNM = '펌프'         THEN PreCnt ELSE 0 END)	[CNT2_PUMP]		
		 , MAX(CASE WHEN TOOLNM = '절단기'       THEN PreCnt ELSE 0 END)	[CNT2_CUTTER]

		 --분실
		 , MAX(CASE WHEN TOOLNM = '죠우13'       THEN [Status] ELSE 0 END)	[CNT3_J013]
		 , MAX(CASE WHEN TOOLNM = '죠우20'       THEN [Status] ELSE 0 END)	[CNT3_J020]
		 , MAX(CASE WHEN TOOLNM = '죠우25'       THEN [Status] ELSE 0 END)	[CNT3_J025]
		 , MAX(CASE WHEN TOOLNM = '죠우30'       THEN [Status] ELSE 0 END)	[CNT3_J030]
		 , MAX(CASE WHEN TOOLNM = '죠우40'       THEN [Status] ELSE 0 END)	[CNT3_J040]
		 , MAX(CASE WHEN TOOLNM = '죠우50'       THEN [Status] ELSE 0 END)	[CNT3_J050]
		 , MAX(CASE WHEN TOOLNM = '죠우60'       THEN [Status] ELSE 0 END)	[CNT3_J060]
		 , MAX(CASE WHEN TOOLNM = '죠우75'       THEN [Status] ELSE 0 END)	[CNT3_J075]
		 , MAX(CASE WHEN TOOLNM = '죠우80'       THEN [Status] ELSE 0 END)	[CNT3_J080]
		 , MAX(CASE WHEN TOOLNM = '죠우100'      THEN [Status] ELSE 0 END)	[CNT3_J100]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN [Status] ELSE 0 END)	[CNT3_CLAMPM]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN [Status] ELSE 0 END)	[CNT3_CLAMPL]
		 , MAX(CASE WHEN TOOLNM = '건중형'       THEN [Status] ELSE 0 END)	[CNT3_GUNM]
		 , MAX(CASE WHEN TOOLNM = '건대형'       THEN [Status] ELSE 0 END)	[CNT3_GUNL]
		 , MAX(CASE WHEN TOOLNM = '실린더중형'   THEN [Status] ELSE 0 END)	[CNT3_CYLINDERM]
		 , MAX(CASE WHEN TOOLNM = '실린더대형'   THEN [Status] ELSE 0 END)	[CNT3_CYLINDERL]
		 , MAX(CASE WHEN TOOLNM = '충전기'       THEN [Status] ELSE 0 END)	[CNT3_CHARGER]
		 , MAX(CASE WHEN TOOLNM = '배터리1'      THEN [Status] ELSE 0 END)	[CNT3_BATTER]
		 , MAX(CASE WHEN TOOLNM = '배터리2'      THEN [Status] ELSE 0 END)	[CNT3_BATTER2]
		 , MAX(CASE WHEN TOOLNM = '펌프'         THEN [Status] ELSE 0 END)	[CNT3_PUMP]		
		 , MAX(CASE WHEN TOOLNM = '절단기'       THEN [Status] ELSE 0 END)	[CNT3_CUTTER]

		 , MAX(CASE WHEN TOOLNM = '죠우13'		 THEN Seq ELSE '' END)		[SEQ_J013]
		 , MAX(CASE WHEN TOOLNM = '죠우20'		 THEN Seq ELSE '' END)		[SEQ_J020]
		 , MAX(CASE WHEN TOOLNM = '죠우25'		 THEN Seq ELSE '' END)		[SEQ_J025]
		 , MAX(CASE WHEN TOOLNM = '죠우30'		 THEN Seq ELSE '' END)		[SEQ_J030]
		 , MAX(CASE WHEN TOOLNM = '죠우40'		 THEN Seq ELSE '' END)		[SEQ_J040]
		 , MAX(CASE WHEN TOOLNM = '죠우50'		 THEN Seq ELSE '' END)		[SEQ_J050]
		 , MAX(CASE WHEN TOOLNM = '죠우60'		 THEN Seq ELSE '' END)		[SEQ_J060]
		 , MAX(CASE WHEN TOOLNM = '죠우75'		 THEN Seq ELSE '' END)		[SEQ_J075]
		 , MAX(CASE WHEN TOOLNM = '죠우80'		 THEN Seq ELSE '' END)		[SEQ_J080]
		 , MAX(CASE WHEN TOOLNM = '죠우100'		 THEN Seq ELSE '' END)		[SEQ_J100]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN Seq ELSE '' END)		[SEQ_CLAMPM]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN Seq ELSE '' END)		[SEQ_CLAMPL]
		 , MAX(CASE WHEN TOOLNM = '건중형'		 THEN Seq ELSE '' END)		[SEQ_GUNM]
		 , MAX(CASE WHEN TOOLNM = '건대형'		 THEN Seq ELSE '' END)		[SEQ_GUNL]
		 , MAX(CASE WHEN TOOLNM = '실린더중형'	 THEN Seq ELSE '' END)		[SEQ_CYLINDERM]
		 , MAX(CASE WHEN TOOLNM = '실린더대형'	 THEN Seq ELSE '' END)		[SEQ_CYLINDERL]
		 , MAX(CASE WHEN TOOLNM = '충전기'		 THEN Seq ELSE '' END)		[SEQ_CHARGER]
		 , MAX(CASE WHEN TOOLNM = '배터리1'		 THEN Seq ELSE '' END)		[SEQ_BATTER]
		 , MAX(CASE WHEN TOOLNM = '배터리2'		 THEN Seq ELSE '' END)		[SEQ_BATTER2]
		 , MAX(CASE WHEN TOOLNM = '펌프'		 THEN Seq ELSE '' END)		[SEQ_PUMP]
		 , MAX(CASE WHEN TOOLNM = '절단기'		 THEN Seq ELSE '' END)		[SEQ_CUTTER]

		 --미회수
		 , MAX(CASE WHEN TOOLNM = '죠우13'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J013]
		 , MAX(CASE WHEN TOOLNM = '죠우20'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J020]
		 , MAX(CASE WHEN TOOLNM = '죠우25'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J025]
		 , MAX(CASE WHEN TOOLNM = '죠우30'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J030]
		 , MAX(CASE WHEN TOOLNM = '죠우40'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J040]
		 , MAX(CASE WHEN TOOLNM = '죠우50'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J050]
		 , MAX(CASE WHEN TOOLNM = '죠우60'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J060]
		 , MAX(CASE WHEN TOOLNM = '죠우75'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J075]
		 , MAX(CASE WHEN TOOLNM = '죠우80'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J080]
		 , MAX(CASE WHEN TOOLNM = '죠우100'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_J100]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_CLAMPM]
		 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_CLAMPL]
		 , MAX(CASE WHEN TOOLNM = '건중형'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_GUNM]
		 , MAX(CASE WHEN TOOLNM = '건대형'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_GUNL]
		 , MAX(CASE WHEN TOOLNM = '실린더중형'	 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_CYLINDERM]
		 , MAX(CASE WHEN TOOLNM = '실린더대형'	 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_CYLINDERL]
		 , MAX(CASE WHEN TOOLNM = '충전기'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_CHARGER]
		 , MAX(CASE WHEN TOOLNM = '배터리1'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_BATTER]
		 , MAX(CASE WHEN TOOLNM = '배터리2'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_BATTER2]
		 , MAX(CASE WHEN TOOLNM = '펌프'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_PUMP]
		 , MAX(CASE WHEN TOOLNM = '절단기'		 THEN UnRecoveredFl ELSE '' END)	[UNRECOVEREDFL_CUTTER]
	FROM ( SELECT DeliveryNo, ContractNo, StandardFee, RebateAmt, ContractFee, DeliveryYmd, CustCd, EquipCustCd, SiteCd, DeliveryPlace, TelNo, Request
				, LotNo, LotCnt, PreExChangeFl, PreCnt, ToolNm, ReturnYmd
				, ROW_NUMBER() OVER(PARTITION BY DeliveryNo, ToolNm ORDER BY PreLotNo, LotNo) RowNum
				, DeliveryGb, DeliveryMoveNo, [Status], DNO, ContractGb, Seq, UnRecoveredFl, ContractYmd, ChargerTypeFL, OilTypeFL, Remark, ISNULL(COMPLTDT,'') AS COMPLTDT
		   FROM ( SELECT DM.DeliveryNo, DM.DeliveryYmd, DM.DeliveryGb, DM.CustCd, DM.EquipCustCd, DM.SiteCd, DM.DeliveryPlace, DM.TelNo, DM.Request
					   , CM.ContractNo, CM.StandardFee, CM.RebateAmt, CM.ContractFee, CM.ContractYmd, CM.ContractGb, CM.ReturnYmd
					   , CM.ChargerTypeFL, CM.OilTypeFL
					   , DD.PreLotNo, DD.Seq
					   , TMO.DeliveryMoveNo, TMO2.DeliveryNo DNO
					   , CASE WHEN R.DeliveryNo IS NOT NULL THEN '회수' ELSE DD.LotNo END LotNo
					   , CASE WHEN Lo.DeliveryNo IS NOT NULL THEN 1 ELSE 0 END [Status]
					   , CASE WHEN R.DeliveryNo IS NOT NULL THEN 0 WHEN Lo.DeliveryNo IS NOT NULL THEN 0 ELSE 1 END LotCnt
					   , DD.PreExChangeFl, ISNULL(DD.UnRecoveredFl, '') UnRecoveredFl
					   --AS후 동일 현장에 재출고되는 건 처리를 위해 PreSeq 관리해야하므로 변경
					   , (CASE WHEN EXISTS(SELECT 1 FROM TODeliveryDetail WHERE DeliveryNo = DD.PreDeliveryNo AND Seq = DD.PreSeq)
								--PreSeq처리 안되는 기존 자료를 위해 남겨둠
								 OR EXISTS(SELECT 1 FROM TODeliveryDetail WHERE DeliveryNo = DD.PreDeliveryNo AND LotNo = DD.PreLotNo) THEN 1 ELSE 0 END) PreCnt
					   , C.ClassCd, C.ClassLNm, C.ClassMNm, C.ClassSNm, C.Spec
					   , dbo.FN_GetToolNm(DD.LotNo) ToolNm
					   --20200903 / 비고란에 주문시의 특이사항 표시
					   --20200904 / 원래로 복원
					   , DM.Remark
					   --20221128 준공일자
					   , MISTO.dbo.fn_getCompltDt(CM.CompltDt,'-') AS COMPLTDT
					   --, x2.SpecialRecord + CASE WHEN x2.SpecialRecord <> '' AND x3.pkgWorkRmk <> '' THEN '/' ELSE '' END + x3.pkgWorkRmk	Remark
				  FROM   TOContractMaster CM
				  INNER JOIN TODeliveryMaster DM ON DM.ContractNo = CM.ContractNo
				  LEFT JOIN TODeliveryDetail DD ON DD.DeliveryNo = DM.DeliveryNo
				  LEFT JOIN ToToolMaster TM ON TM.LotNo = DD.LotNo AND TM.ItemGrpCd = DD.ItemGrpCd
				  LEFT JOIN TOClass C ON C.ClassCd = TM.ClassCd AND C.ItemGrpCd = TM.ItemGrpCD
				  LEFT JOIN TORecovery R ON R.DeliveryNo = DD.DeliveryNo AND R.Seq = DD.Seq AND R.LotNo = DD.LotNo
				  LEFT JOIN TOMove TMO ON TMO.DeliveryMoveNo = DD.DeliveryNo
				  LEFT JOIN TOMove TMO2 ON TMO2.DeliveryNo = DD.DeliveryNo
				  LEFT JOIN ToLoss Lo ON Lo.DeliveryNo = DD.DeliveryNo AND Lo.Seq = DD.Seq AND Lo.CancelYN != 'Y'
				  --20200903 / 비고란에 주문시의 특이사항 표시
				  --LEFT JOIN MISTW.dbo.tbGtcRentSet x1 ON x1.DeliveryNo = DM.DeliveryNo
				  --LEFT JOIN MISTW.dbo.tbGtcOrdEnr x2 ON x2.OrdEnrNo = x1.OrdEnrNo
				  --LEFT JOIN MISTW.dbo.tbGtcPkgEnr x3 ON x3.OrdEnrNo = x2.OrdEnrNo
				  WHERE  CM.ItemGrpCd = 'SP' 
				  AND	 CASE WHEN @iDeliveryGb = '' THEN '01' ELSE @iDeliveryGb END = '01'
				  AND    CM.CustCd LIKE @iCustCd + '%'
				  AND    CM.EquipCustCd LIKE @iEquipCustCd + '%'
				  AND    CM.SiteCd LIKE @iSiteCD + '%'
				  AND    CM.ContractGb IN (SELECT VALUE FROM MISCM.dbo.FN_TOSPLIT(@iContractGB, ','))	--현장출고, 계약종료
				  UNION ALL
				  SELECT DM.DeliveryNo, DM.DeliveryYmd, DM.DeliveryGb, DM.CustCd, DM.EquipCustCd, DM.SiteCd, DM.DeliveryPlace, DM.TelNo, DM.Request
					   , '', 0, 0, 0, '', '', ''
					   , 'N', 'N'
					   , DD.PreLotNo, DD.Seq
					   , NULL, ''
					   , CASE WHEN R.DeliveryNo IS NOT NULL THEN '회수' WHEN TM.Status = '09' THEN '분실' ELSE DD.LotNo END LotNo
					   , 0
					   , CASE WHEN R.DeliveryNo IS NOT NULL THEN 0 ELSE 1 END LotCnt
					   , DD.PreExChangeFl, ISNULL(DD.UnRecoveredFl, '') UnRecoveredFl
					   , 0
					   , C.ClassCd, C.ClassLNm, C.ClassMNm, C.ClassSNm, C.Spec
					   , dbo.FN_GetToolNm(DD.LotNo) ToolNm
					   --20200903 / 비고란에 주문시의 특이사항 표시
					   --20200904 / 원래로 복원
					   , DM.Remark
					   , ''
					   --, x2.SpecialRecord + CASE WHEN x2.SpecialRecord <> '' AND x3.pkgWorkRmk <> '' THEN '/' ELSE '' END + x3.pkgWorkRmk	Remark
				  FROM   TODeliveryMaster DM
				  INNER JOIN TODeliveryDetail DD ON DD.DeliveryNo = DM.DeliveryNo
				  INNER JOIN TOToolMaster TM ON TM.LotNo = DD.LotNo AND TM.ItemGrpCd = DD.ItemGrpCd
				  INNER JOIN TOClass C ON C.ClassCd = TM.ClassCd AND C.ItemGrpCd = TM.ItemGrpCd
				  LEFT JOIN TORecovery R ON R.DeliveryNo = DD.DeliveryNo AND R.Seq = DD.Seq
				  --20200903 / 비고란에 주문시의 특이사항 표시
				  --LEFT JOIN MISTW.dbo.tbGtcOrdEnr x2 ON x2.DeliveryNo = DM.DeliveryNo
				  --LEFT JOIN MISTW.dbo.tbGtcPkgEnr x3 ON x3.OrdEnrNo = x2.OrdEnrNo
				  WHERE  DM.ItemGrpCd = 'SP' 
				  AND	 DM.DeliveryGb = '02'
				  AND    @iDeliveryGb = '02'
				  AND    C.StatusUnUseFl ='N' -- 20190318	영선씨 요청으로 판매는 소모품 안보이게 처리
				  AND    DM.CustCd = CASE WHEN @iCustCd = '' THEN DM.CustCd ELSE @iCustCd END
				  AND    DM.EquipCustCd = CASE WHEN @iEquipCustCd = '' THEN DM.EquipCustCd ELSE @iEquipCustCd END
				  AND    DM.SiteCd = CASE WHEN @iSiteCD = '' THEN DM.SiteCd ELSE @iSiteCD END
				  ) T
		 ) A
	WHERE  CustCd LIKE @iCustCd + '%'
	AND    EquipCustCd LIKE @iEquipCustCd + '%'
	AND    SiteCd LIKE @iSiteCD + '%'
	AND    CASE WHEN @iDeliveryGb = '02' OR RowNum = 1 THEN DeliveryGb ELSE '03' END LIKE @iGubun
	GROUP BY DeliveryNo, RowNum, CustCd, DeliveryGb, COMPLTDT
	ORDER BY DeliveryYmd
	--ORDER BY DeliveryNo

/*	
	SELECT DELIVERYNO
		,  MAX(CONTRACTNO) AS CONTRACTNO
		--20161219 강윤철 충전식,유압식 체크위해 추가(CONTRACTYMD)
		,  SUBSTRING(MAX(CONTRACTYMD),1,4) + '-' + SUBSTRING(MAX(CONTRACTYMD),5,2) + '-' + SUBSTRING(MAX(CONTRACTYMD),7,2) AS CONTRACTYMD
		--20161219 강윤철 충전식,유압식 체크위해 추가(CHARGERTYPEFL)
		,  CASE WHEN ROWNUM = 1 THEN MAX(ISNULL(CHARGERTYPEFL,'N')) ELSE 'N' END	CHARGERTYPEFL
		--20161219 강윤철 충전식,유압식 체크위해 추가(OILTYPEFL)
		,  CASE WHEN ROWNUM = 1 THEN MAX(ISNULL(OILTYPEFL,'N')) ELSE 'N' END	OILTYPEFL
		,  MAX(STANDARDFEE) AS STANDARDFEE
		,  MAX(REBATEAMT)	AS REBATEAMT
		,  MAX(CONTRACTFEE) AS CONTRACTFEE		
		,  CASE WHEN ROWNUM = 1 THEN MAX(CONTRACTFEE) ELSE 0 END CONTRACTFEE_D
		,  CASE WHEN ROWNUM = 1 THEN DELIVERYGB ELSE '03' END DELIVERYGB
		,  SUBSTRING(MAX(DELIVERYYMD),1,4) + '-' + SUBSTRING(MAX(DELIVERYYMD),5,2) + '-' + SUBSTRING(MAX(DELIVERYYMD),7,2) AS DELIVERYYMD
		,  SUBSTRING(MAX(RETURNYMD),1,4) + '-' + SUBSTRING(MAX(RETURNYMD),5,2) + '-' + SUBSTRING(MAX(RETURNYMD),7,2) AS RETURNYMD
		,  CUSTCD CUSTCD
		,  MAX(A.EQUIPCUSTCD) EQUIPCUSTCD
		,  MAX(A.SITECD) SITECD
		,  (SELECT X.CUSTNM FROM MISSA..SACUST X WHERE X.DUZCUSTCD = A.CUSTCD) AS CUSTNM
		,  (SELECT X.BUSINESSNM FROM MISSA..SABusiness x WHERE x.BusinessCd = MAX(A.EQUIPCUSTCD)) AS EQUIPCUSTNM
		,  (SELECT X.SITENM FROM TOSIte X WHERE x.SiteCd = MAX(A.SITECD)) AS SITENM
		,  (SELECT X.PlaceNm FROM TOPlace X WHERE x.PlaceCd = MAX(DELIVERYPLACE)) DELIVERYPLACE
		--20161221 강윤철 전달사항이 AS에는 나타나지 않도록 수정
		,  CASE WHEN ROWNUM = 1 THEN MAX(REQUEST) ELSE '' END	AS REQUEST
		--,  MAX(REQUEST)			AS REQUEST
		,  MAX(TELNO)			AS TELNO
		,  MAX(DELIVERYMOVENO)	AS DELIVERYMOVENO
		,  MAX(DNO)				AS DNO
		,  MAX(CONTRACTGB)		AS CONTRACTGB
		,  SUM(LOTCNT)			AS [TOTAL]
		,  MAX(CASE WHEN TOOLNM = '죠우13' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J013]
		,  MAX(CASE WHEN TOOLNM = '죠우20' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J020]
		,  MAX(CASE WHEN TOOLNM = '죠우25' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J025]
		,  MAX(CASE WHEN TOOLNM = '죠우30' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J030]
		,  MAX(CASE WHEN TOOLNM = '죠우40' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J040]
		,  MAX(CASE WHEN TOOLNM = '죠우50' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J050]
		,  MAX(CASE WHEN TOOLNM = '죠우60' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J060]
		,  MAX(CASE WHEN TOOLNM = '죠우75' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J075]
		,  MAX(CASE WHEN TOOLNM = '죠우80' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J080]
		,  MAX(CASE WHEN TOOLNM = '죠우100' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_J100]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN PREEXCHANGEFL ELSE '' END)	[EXCHANGE_CLAMPM]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN PREEXCHANGEFL ELSE '' END)	[EXCHANGE_CLAMPL]
		,  MAX(CASE WHEN TOOLNM = '건중형' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_GUNM]
		,  MAX(CASE WHEN TOOLNM = '건대형' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_GUNL]
		,  MAX(CASE WHEN TOOLNM = '실린더중형' THEN PREEXCHANGEFL ELSE '' END)		[EXCHANGE_CYLINDERM]
		,  MAX(CASE WHEN TOOLNM = '실린더대형' THEN PREEXCHANGEFL ELSE '' END)		[EXCHANGE_CYLINDERL]
		,  MAX(CASE WHEN TOOLNM = '충전기' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_CHARGER]
		,  MAX(CASE WHEN TOOLNM = '배터리1' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_BATTER]
		,  MAX(CASE WHEN TOOLNM = '배터리2' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_BATTER2]
		,  MAX(CASE WHEN TOOLNM = '펌프' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_PUMP]
		,  MAX(CASE WHEN TOOLNM = '절단기' THEN PREEXCHANGEFL ELSE '' END)			[EXCHANGE_CUTTER]

		,  MAX(CASE WHEN TOOLNM = '죠우13' THEN LOTNO ELSE '' END)			[J013]
		,  MAX(CASE WHEN TOOLNM = '죠우20' THEN LOTNO ELSE '' END)			[J020]
		,  MAX(CASE WHEN TOOLNM = '죠우25' THEN LOTNO ELSE '' END)			[J025]
		,  MAX(CASE WHEN TOOLNM = '죠우30' THEN LOTNO ELSE '' END)			[J030]
		,  MAX(CASE WHEN TOOLNM = '죠우40' THEN LOTNO ELSE '' END)			[J040]
		,  MAX(CASE WHEN TOOLNM = '죠우50' THEN LOTNO ELSE '' END)			[J050]
		,  MAX(CASE WHEN TOOLNM = '죠우60' THEN LOTNO ELSE '' END)			[J060]
		,  MAX(CASE WHEN TOOLNM = '죠우75' THEN LOTNO ELSE '' END)			[J075]
		,  MAX(CASE WHEN TOOLNM = '죠우80' THEN LOTNO ELSE '' END)			[J080]
		,  MAX(CASE WHEN TOOLNM = '죠우100' THEN LOTNO ELSE '' END)			[J100]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN LOTNO ELSE '' END)	[CLAMPM]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN LOTNO ELSE '' END)	[CLAMPL]
		,  MAX(CASE WHEN TOOLNM = '건중형' THEN LOTNO ELSE '' END)			[GUNM]
		,  MAX(CASE WHEN TOOLNM = '건대형' THEN LOTNO ELSE '' END)			[GUNL]
		,  MAX(CASE WHEN TOOLNM = '실린더중형' THEN LOTNO ELSE '' END)		[CYLINDERM]
		,  MAX(CASE WHEN TOOLNM = '실린더대형' THEN LOTNO ELSE '' END)		[CYLINDERL]
		,  MAX(CASE WHEN TOOLNM = '충전기' THEN LOTNO ELSE '' END)			[CHARGER]
		,  MAX(CASE WHEN TOOLNM = '배터리1' THEN LOTNO ELSE '' END)			[BATTER]
		,  MAX(CASE WHEN TOOLNM = '배터리2' THEN LOTNO ELSE '' END)			[BATTER2]
		,  MAX(CASE WHEN TOOLNM = '펌프' THEN LOTNO ELSE '' END)			[PUMP]
		,  MAX(CASE WHEN TOOLNM = '절단기' THEN LOTNO ELSE '' END)			[CUTTER]
		

		,  MAX(CASE WHEN TOOLNM = '죠우13' THEN LOTCNT ELSE 0 END)			[CNT_J013]
		,  MAX(CASE WHEN TOOLNM = '죠우20' THEN LOTCNT ELSE 0 END)			[CNT_J020]
		,  MAX(CASE WHEN TOOLNM = '죠우25' THEN LOTCNT ELSE 0 END)			[CNT_J025]
		,  MAX(CASE WHEN TOOLNM = '죠우30' THEN LOTCNT ELSE 0 END)			[CNT_J030]
		,  MAX(CASE WHEN TOOLNM = '죠우40' THEN LOTCNT ELSE 0 END)			[CNT_J040]
		,  MAX(CASE WHEN TOOLNM = '죠우50' THEN LOTCNT ELSE 0 END)			[CNT_J050]
		,  MAX(CASE WHEN TOOLNM = '죠우60' THEN LOTCNT ELSE 0 END)			[CNT_J060]
		,  MAX(CASE WHEN TOOLNM = '죠우75' THEN LOTCNT ELSE 0 END)			[CNT_J075]
		,  MAX(CASE WHEN TOOLNM = '죠우80' THEN LOTCNT ELSE 0 END)			[CNT_J080]
		,  MAX(CASE WHEN TOOLNM = '죠우100' THEN LOTCNT ELSE 0 END)			[CNT_J100]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN LOTCNT ELSE 0 END)	[CNT_CLAMPM]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN LOTCNT ELSE 0 END)	[CNT_CLAMPL]
		,  MAX(CASE WHEN TOOLNM = '건중형' THEN LOTCNT ELSE 0 END)			[CNT_GUNM]
		,  MAX(CASE WHEN TOOLNM = '건대형' THEN LOTCNT ELSE 0 END)			[CNT_GUNL]
		,  MAX(CASE WHEN TOOLNM = '실린더중형' THEN LOTCNT ELSE 0 END)		[CNT_CYLINDERM]
		,  MAX(CASE WHEN TOOLNM = '실린더대형' THEN LOTCNT ELSE 0 END)		[CNT_CYLINDERL]
		,  SUM(CASE WHEN TOOLNM = '충전기' THEN LOTCNT ELSE 0 END)			[CNT_CHARGER]
		,  SUM(CASE WHEN TOOLNM = '배터리1' THEN LOTCNT ELSE 0 END)			[CNT_BATTER]
		,  SUM(CASE WHEN TOOLNM = '배터리2' THEN LOTCNT ELSE 0 END)			[CNT_BATTER2]
		,  MAX(CASE WHEN TOOLNM = '펌프' THEN LOTCNT ELSE 0 END)			[CNT_PUMP]		
		,  MAX(CASE WHEN TOOLNM = '절단기' THEN LOTCNT ELSE 0 END)			[CNT_CUTTER]

		,  MAX(CASE WHEN TOOLNM = '죠우13' THEN PRECNT ELSE 0 END)			[CNT2_J013]
		,  MAX(CASE WHEN TOOLNM = '죠우20' THEN PRECNT ELSE 0 END)			[CNT2_J020]
		,  MAX(CASE WHEN TOOLNM = '죠우25' THEN PRECNT ELSE 0 END)			[CNT2_J025]
		,  MAX(CASE WHEN TOOLNM = '죠우30' THEN PRECNT ELSE 0 END)			[CNT2_J030]
		,  MAX(CASE WHEN TOOLNM = '죠우40' THEN PRECNT ELSE 0 END)			[CNT2_J040]
		,  MAX(CASE WHEN TOOLNM = '죠우50' THEN PRECNT ELSE 0 END)			[CNT2_J050]
		,  MAX(CASE WHEN TOOLNM = '죠우60' THEN PRECNT ELSE 0 END)			[CNT2_J060]
		,  MAX(CASE WHEN TOOLNM = '죠우75' THEN PRECNT ELSE 0 END)			[CNT2_J075]
		,  MAX(CASE WHEN TOOLNM = '죠우80' THEN PRECNT ELSE 0 END)			[CNT2_J080]
		,  MAX(CASE WHEN TOOLNM = '죠우100' THEN PRECNT ELSE 0 END)			[CNT2_J100]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN PRECNT ELSE 0 END)	[CNT2_CLAMPM]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN PRECNT ELSE 0 END)	[CNT2_CLAMPL]
		,  MAX(CASE WHEN TOOLNM = '건중형' THEN PRECNT ELSE 0 END)			[CNT2_GUNM]
		,  MAX(CASE WHEN TOOLNM = '건대형' THEN PRECNT ELSE 0 END)			[CNT2_GUNL]
		,  MAX(CASE WHEN TOOLNM = '실린더중형' THEN PRECNT ELSE 0 END)		[CNT2_CYLINDERM]
		,  MAX(CASE WHEN TOOLNM = '실린더대형' THEN PRECNT ELSE 0 END)		[CNT2_CYLINDERL]
		,  MAX(CASE WHEN TOOLNM = '충전기' THEN PRECNT ELSE 0 END)			[CNT2_CHARGER]
		,  MAX(CASE WHEN TOOLNM = '배터리1' THEN PRECNT ELSE 0 END)			[CNT2_BATTER]		
		,  MAX(CASE WHEN TOOLNM = '배터리2' THEN PRECNT ELSE 0 END)			[CNT2_BATTER2]
		,  MAX(CASE WHEN TOOLNM = '펌프' THEN PRECNT ELSE 0 END)			[CNT2_PUMP]		
		,  MAX(CASE WHEN TOOLNM = '절단기' THEN PRECNT ELSE 0 END)			[CNT2_CUTTER]
		
		,  MAX(CASE WHEN TOOLNM = '죠우13' THEN STATUS ELSE 0 END)			[CNT3_J013]
		,  MAX(CASE WHEN TOOLNM = '죠우20' THEN STATUS ELSE 0 END)			[CNT3_J020]
		,  MAX(CASE WHEN TOOLNM = '죠우25' THEN STATUS ELSE 0 END)			[CNT3_J025]
		,  MAX(CASE WHEN TOOLNM = '죠우30' THEN STATUS ELSE 0 END)			[CNT3_J030]
		,  MAX(CASE WHEN TOOLNM = '죠우40' THEN STATUS ELSE 0 END)			[CNT3_J040]
		,  MAX(CASE WHEN TOOLNM = '죠우50' THEN STATUS ELSE 0 END)			[CNT3_J050]
		,  MAX(CASE WHEN TOOLNM = '죠우60' THEN STATUS ELSE 0 END)			[CNT3_J060]
		,  MAX(CASE WHEN TOOLNM = '죠우75' THEN STATUS ELSE 0 END)			[CNT3_J075]
		,  MAX(CASE WHEN TOOLNM = '죠우80' THEN STATUS ELSE 0 END)			[CNT3_J080]
		,  MAX(CASE WHEN TOOLNM = '죠우100' THEN STATUS ELSE 0 END)			[CNT3_J100]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN STATUS ELSE 0 END)	[CNT3_CLAMPM]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN STATUS ELSE 0 END)	[CNT3_CLAMPL]
		,  MAX(CASE WHEN TOOLNM = '건중형' THEN STATUS ELSE 0 END)			[CNT3_GUNM]
		,  MAX(CASE WHEN TOOLNM = '건대형' THEN STATUS ELSE 0 END)			[CNT3_GUNL]
		,  MAX(CASE WHEN TOOLNM = '실린더중형' THEN STATUS ELSE 0 END)		[CNT3_CYLINDERM]
		,  MAX(CASE WHEN TOOLNM = '실린더대형' THEN STATUS ELSE 0 END)		[CNT3_CYLINDERL]
		,  MAX(CASE WHEN TOOLNM = '충전기' THEN STATUS ELSE 0 END)			[CNT3_CHARGER]
		,  MAX(CASE WHEN TOOLNM = '배터리1' THEN STATUS ELSE 0 END)			[CNT3_BATTER]
		,  MAX(CASE WHEN TOOLNM = '배터리2' THEN STATUS ELSE 0 END)			[CNT3_BATTER2]
		,  MAX(CASE WHEN TOOLNM = '펌프' THEN STATUS ELSE 0 END)			[CNT3_PUMP]		
		,  MAX(CASE WHEN TOOLNM = '절단기' THEN STATUS ELSE 0 END)			[CNT3_CUTTER]

		,  MAX(CASE WHEN TOOLNM = '죠우13'			THEN SEQ ELSE '' END)		[SEQ_J013]
		,  MAX(CASE WHEN TOOLNM = '죠우20'			THEN SEQ ELSE '' END)		[SEQ_J020]
		,  MAX(CASE WHEN TOOLNM = '죠우25'			THEN SEQ ELSE '' END)		[SEQ_J025]
		,  MAX(CASE WHEN TOOLNM = '죠우30'			THEN SEQ ELSE '' END)		[SEQ_J030]
		,  MAX(CASE WHEN TOOLNM = '죠우40'			THEN SEQ ELSE '' END)		[SEQ_J040]
		,  MAX(CASE WHEN TOOLNM = '죠우50'			THEN SEQ ELSE '' END)		[SEQ_J050]
		,  MAX(CASE WHEN TOOLNM = '죠우60'			THEN SEQ ELSE '' END)		[SEQ_J060]
		,  MAX(CASE WHEN TOOLNM = '죠우75'			THEN SEQ ELSE '' END)		[SEQ_J075]
		,  MAX(CASE WHEN TOOLNM = '죠우80'			THEN SEQ ELSE '' END)		[SEQ_J080]
		,  MAX(CASE WHEN TOOLNM = '죠우100'			THEN SEQ ELSE '' END)		[SEQ_J100]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴중형'	THEN SEQ ELSE '' END)		[SEQ_CLAMPM]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴대형'	THEN SEQ ELSE '' END)		[SEQ_CLAMPL]
		,  MAX(CASE WHEN TOOLNM = '건중형'			THEN SEQ ELSE '' END)		[SEQ_GUNM]
		,  MAX(CASE WHEN TOOLNM = '건대형'			THEN SEQ ELSE '' END)		[SEQ_GUNL]
		,  MAX(CASE WHEN TOOLNM = '실린더중형'		THEN SEQ ELSE '' END)		[SEQ_CYLINDERM]
		,  MAX(CASE WHEN TOOLNM = '실린더대형'		THEN SEQ ELSE '' END)		[SEQ_CYLINDERL]
		,  MAX(CASE WHEN TOOLNM = '충전기'			THEN SEQ ELSE '' END)		[SEQ_CHARGER]
		,  MAX(CASE WHEN TOOLNM = '배터리1'			THEN SEQ ELSE '' END)		[SEQ_BATTER]
		,  MAX(CASE WHEN TOOLNM = '배터리2'			THEN SEQ ELSE '' END)		[SEQ_BATTER2]
		,  MAX(CASE WHEN TOOLNM = '펌프'			THEN SEQ ELSE '' END)		[SEQ_PUMP]
		,  MAX(CASE WHEN TOOLNM = '절단기'			THEN SEQ ELSE '' END)		[SEQ_CUTTER]

		,  MAX(CASE WHEN TOOLNM = '죠우13'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J013]
		,  MAX(CASE WHEN TOOLNM = '죠우20'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J020]
		,  MAX(CASE WHEN TOOLNM = '죠우25'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J025]
		,  MAX(CASE WHEN TOOLNM = '죠우30'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J030]
		,  MAX(CASE WHEN TOOLNM = '죠우40'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J040]
		,  MAX(CASE WHEN TOOLNM = '죠우50'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J050]
		,  MAX(CASE WHEN TOOLNM = '죠우60'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J060]
		,  MAX(CASE WHEN TOOLNM = '죠우75'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J075]
		,  MAX(CASE WHEN TOOLNM = '죠우80'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J080]
		,  MAX(CASE WHEN TOOLNM = '죠우100'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_J100]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴중형'	THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_CLAMPM]
		,  MAX(CASE WHEN TOOLNM = '클램핑툴대형'	THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_CLAMPL]
		,  MAX(CASE WHEN TOOLNM = '건중형'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_GUNM]
		,  MAX(CASE WHEN TOOLNM = '건대형'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_GUNL]
		,  MAX(CASE WHEN TOOLNM = '실린더중형'		THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_CYLINDERM]
		,  MAX(CASE WHEN TOOLNM = '실린더대형'		THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_CYLINDERL]
		,  MAX(CASE WHEN TOOLNM = '충전기'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_CHARGER]
		,  MAX(CASE WHEN TOOLNM = '배터리1'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_BATTER]
		,  MAX(CASE WHEN TOOLNM = '배터리2'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_BATTER2]
		,  MAX(CASE WHEN TOOLNM = '펌프'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_PUMP]
		,  MAX(CASE WHEN TOOLNM = '절단기'			THEN UNRECOVEREDFL ELSE '' END)		[UNRECOVEREDFL_CUTTER]
	  FROM (
			SELECT DELIVERYNO,CONTRACTNO,STANDARDFEE,REBATEAMT,CONTRACTFEE,DELIVERYYMD,CUSTCD,LOTNO,LOTCNT,PREEXCHANGEFL,PRECNT,TOOLNM,EQUIPCUSTCD,SITECD,DELIVERYPLACE,TELNO,REQUEST,RETURNYMD
				 
				 , ROW_NUMBER() OVER(PARTITION BY DELIVERYNO,TOOLNM ORDER BY PreLotNo,LOTNO) ROWNUM
				 --, ROW_NUMBER() OVER(PARTITION BY DELIVERYNO,TOOLNM,LOTNO ORDER BY PreLotNo,LOTNO) ROWNUM
				 , DELIVERYGB,DELIVERYMOVENO,[STATUS], DNO, CONTRACTGB,SEQ,UNRECOVEREDFL
				 --20161219 강윤철 충전식,유압식 체크위해 추가
				 , CONTRACTYMD, CHARGERTYPEFL, OILTYPEFL
			  FROM (
					SELECT DM.DELIVERYNO, DM.DELIVERYYMD, DM.DELIVERYGB, DM.CUSTCD, DM.EQUIPCUSTCD, DM.SITECD, DM.DELIVERYPLACE, DM.TELNO, DM.REQUEST
						 , CM.CONTRACTNO,CM.STANDARDFEE,CM.REBATEAMT,CM.CONTRACTFEE, CM.CONTRACTYMD, TMO.DELIVERYMOVENO, DD.PRELOTNO, DD.SEQ, TMO2.DeliveryNo DNO, CM.CONTRACTGB, CM.RETURNYMD
						 --20161219 강윤철 충전식,유압식 체크위해 추가
						 , CM.CHARGERTYPEFL, CM.OILTYPEFL
						 , CASE WHEN R.DeliveryNo IS NOT NULL THEN '회수' ELSE TM.LOTNO END LOTNO
						 , CASE WHEN Lo.DeliveryNo IS NOT NULL THEN 1 ELSE 0 END [STATUS]
						 , CASE WHEN R.DeliveryNo IS NOT NULL THEN 0 WHEN Lo.DeliveryNo IS NOT NULL THEN 0 ELSE 1 END LOTCNT
						 , DD.PREEXCHANGEFL, ISNULL(DD.UnRecoveredFl,'') UNRECOVEREDFL	
						 , (CASE WHEN EXISTS (SELECT 1 FROM TODeliveryDetail x WHERE x.DeliveryNo = Dm.DeliveryNo AND dd.PreLotNo = x.LotNo) THEN 1 ELSE 0 END) AS PRECNT		  
						 , C.CLASSCD, C.CLASSLNM, C.CLASSMNM, C.CLASSSNM, C.SPEC
						 , CASE  WHEN C.CLASSLNM = '죠우' AND C.SPEC = '13' THEN '죠우13'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '20' THEN '죠우20'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '25' THEN '죠우25'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '30' THEN '죠우30'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '40' THEN '죠우40'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '50' THEN '죠우50'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '60' THEN '죠우60'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '75' THEN '죠우75'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '80' THEN '죠우80'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '100' THEN '죠우100'
								 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '중형' THEN '클램핑툴중형'			 
								 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '대형' THEN '클램핑툴대형'
								 WHEN C.CLASSLNM = '대형건' THEN '건대형'
								 WHEN C.CLASSLNM = '충전건' AND C.CLASSSNM IN ('구형','신형') THEN '건중형'
								 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '중형' THEN '실린더중형'
								 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '대형' THEN '실린더대형'
								 WHEN C.CLASSSNM = '충전기' THEN '충전기' 
								 WHEN C.CLASSSNM = '배터리' AND RIGHT(DD.LotNo,2) ='01' THEN '배터리1'
								 WHEN C.CLASSSNM = '배터리' AND RIGHT(DD.LotNo,2) ='02' THEN '배터리2'
								 WHEN C.CLASSLNM = '전동펌프' THEN '펌프'
								 WHEN C.CLASSLNM = '절단기' THEN '절단기'
							ELSE '' END TOOLNM
					  FROM TOCONTRACTMASTER CM
					  JOIN TODELIVERYMASTER DM ON DM.CONTRACTNO = CM.CONTRACTNO
					  JOIN TODELIVERYDETAIL DD ON DD.DELIVERYNO = DM.DELIVERYNO --AND DD.PRELOTNO = ''  
					  JOIN TOToolMaster TM ON TM.LotNo = dd.LotNo
					  JOIN TOCLASS C ON C.CLASSCD = TM.CLASSCD					  
					  LEFT JOIN TORecovery R ON R.DeliveryNo = DD.DeliveryNo AND R.LotNo = DD.LotNo AND R.Seq = DD.Seq
					  LEFT JOIN TOMove TMO ON TMO.DeliveryMoveNo = DD.DeliveryNo
					  LEFT JOIN TOMove TMO2 ON TMO2.DeliveryNo = DD.DeliveryNo
					  LEFT JOIN ToLoss Lo ON Lo.DeliveryNo = DD.DeliveryNo AND CancelYN != 'Y' AND Lo.Seq = DD.Seq
					 WHERE '01' = CASE WHEN @iDeliveryGb = '' THEN '01' ELSE @iDeliveryGb END
					   AND CM.CUSTCD LIKE @ICUSTCD + '%' -- = CASE WHEN @ICUSTCD = '' THEN CM.CUSTCD ELSE @ICUSTCD END
					   AND CM.EQUIPCUSTCD LIKE @iEquipCustCd + '%'  -- = CASE WHEN @iEquipCustCd = '' THEN CM.EQUIPCUSTCD ELSE @iEquipCustCd END
					   AND CM.SITECD LIKE @iSiteCD + '%' --  = CASE WHEN @iSiteCD = '' THEN CM.SITECD ELSE @iSiteCD END
					   AND CM.CONTRACTGB IN (SELECT VALUE
											   FROM MISCM..FN_TOSPLIT(@iContractGB,',')
											)	--현장출고, 계약종료
					   --AND DM.DeliveryGb = @iDeliveryGb
					 UNION ALL
					SELECT DM.DELIVERYNO, DM.DELIVERYYMD, DM.DELIVERYGB, DM.CUSTCD, DM.EQUIPCUSTCD, DM.SITECD, DM.DELIVERYPLACE, DM.TELNO, DM.REQUEST
						 , '',0,0,0,'',NULL, DD.PRELOTNO, DD.SEQ, '', '',''
						 --20161219 강윤철 충전식,유압식 체크위해 추가
						 , 'N','N'
						 , CASE WHEN R.DeliveryNo IS NOT NULL THEN '회수' WHEN TM.Status = '09' THEN '분실' ELSE TM.LOTNO END LOTNO
						 , 0 AS STATUS
						 , CASE WHEN R.DeliveryNo IS NOT NULL THEN 0 ELSE 1 END LOTCNT
						 , DD.PREEXCHANGEFL
						 , ISNULL(DD.UnRecoveredFl,'') UNRECOVEREDFL	
						 , 0 AS PRECNT
						 , C.CLASSCD, C.CLASSLNM, C.CLASSMNM, C.CLASSSNM, C.SPEC
						 , CASE  WHEN C.CLASSLNM = '죠우' AND C.SPEC = '13' THEN '죠우13'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '20' THEN '죠우20'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '25' THEN '죠우25'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '30' THEN '죠우30'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '40' THEN '죠우40'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '50' THEN '죠우50'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '60' THEN '죠우60'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '75' THEN '죠우75'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '80' THEN '죠우80'
								 WHEN C.CLASSLNM = '죠우' AND C.SPEC = '100' THEN '죠우100'
								 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '중형' THEN '클램핑툴중형'			 
								 WHEN C.CLASSLNM = '클램핑툴' AND C.SPEC = '대형' THEN '클램핑툴대형'
								 WHEN C.CLASSLNM = '대형건' THEN '건대형'
								 WHEN C.CLASSLNM = '충전건' AND C.CLASSSNM IN ('구형','신형') THEN '건중형'
								 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '중형' THEN '실린더중형'
								 WHEN C.CLASSLNM = '실린더' AND C.SPEC = '대형' THEN '실린더대형'
								 WHEN C.CLASSSNM = '충전기' THEN '충전기'
								 WHEN C.CLASSSNM = '배터리' AND RIGHT(DD.LotNo,2) ='01' THEN '배터리1'
								 WHEN C.CLASSSNM = '배터리' AND RIGHT(DD.LotNo,2) ='02' THEN '배터리2'
								 WHEN C.CLASSLNM = '전동펌프' THEN '펌프'
								 WHEN C.CLASSLNM = '절단기' THEN '절단기'
							ELSE '' END TOOLNM
					  FROM TODELIVERYMASTER DM 
					  JOIN TODELIVERYDETAIL DD ON DD.DELIVERYNO = DM.DELIVERYNO --AND DD.PRELOTNO = ''  
					  JOIN TOToolMaster TM ON TM.LotNo = dd.LotNo
					  JOIN TOCLASS C ON C.CLASSCD = TM.CLASSCD					  
					  LEFT JOIN TORecovery R ON R.DeliveryNo = DD.DeliveryNo AND R.Seq = DD.Seq
					 WHERE DM.DeliveryGb = '02'
					   AND @iDeliveryGb = '02'
					   AND DM.CUSTCD = CASE WHEN @ICUSTCD = '' THEN DM.CUSTCD ELSE @ICUSTCD END
					   AND DM.EQUIPCUSTCD = CASE WHEN @iEquipCustCd = '' THEN DM.EQUIPCUSTCD ELSE @iEquipCustCd END
					   AND DM.SITECD = CASE WHEN @iSiteCD = '' THEN DM.SITECD ELSE @iSiteCD END
				) A
			) A
	 WHERE CUSTCD LIKE @iCustCd + '%' -- = CASE WHEN @ICUSTCD = '' THEN CM.CUSTCD ELSE @ICUSTCD END
	   AND EQUIPCUSTCD LIKE @iEquipCustCd + '%'  -- = CASE WHEN @iEquipCustCd = '' THEN CM.EQUIPCUSTCD ELSE @iEquipCustCd END
	   AND SITECD LIKE @iSiteCD + '%' --  = CASE WHEN @iSiteCD = '' THEN CM.SITECD ELSE @iSiteCD END
	 GROUP BY DELIVERYNO, ROWNUM, CUSTCD,DELIVERYGB
	 ORDER BY DELIVERYNO -- CUSTNM, EQUIPCUSTNM, SITENM, DELIVERYYMD
*/



END
```


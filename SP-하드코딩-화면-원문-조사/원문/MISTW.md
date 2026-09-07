# MISTW SP 전체 원문

우선 조사 대상 SP 26개. 각 SP의 화면·route 호출 정보와 원문 상태를 머리말에 적고, 아래 fenced code block은 선택한 원문 파일 전체다.

| SP | 화면/route 수 | 원문 기준 |
| --- | ---: | --- |
| [`proc_PURE002_S_01`](#mistw-proc-pure002-s-01) | 0 | be_anasa develop 원문 |
| [`proc_PURE002_S_02`](#mistw-proc-pure002-s-02) | 0 | be_anasa develop 원문 |
| [`proc_LOGB005_S_03_V2`](#mistw-proc-logb005-s-03-v2) | 0 | 운영 snapshot 원문 우선 |
| [`proc_LOGB009_S_GP_ORD_V2`](#mistw-proc-logb009-s-gp-ord-v2) | 0 | 운영 snapshot 원문 우선 |
| [`proc_LOGE002_S_01`](#mistw-proc-loge002-s-01) | 0 | 운영 snapshot 원문 우선 |
| [`proc_LOGE002_S_02`](#mistw-proc-loge002-s-02) | 0 | 운영 snapshot 원문 우선 |
| [`proc_LOGE002_S_03`](#mistw-proc-loge002-s-03) | 0 | 운영 snapshot 원문 우선 |
| [`proc_LOGE002_S_04`](#mistw-proc-loge002-s-04) | 0 | 운영 snapshot 원문 우선 |
| [`proc_LOGG023_S_01`](#mistw-proc-logg023-s-01) | 0 | be_anasa develop 원문 |
| [`proc_LOGG022_S_06`](#mistw-proc-logg022-s-06) | 0 | 운영 snapshot 원문 우선 |
| [`proc_LOGC001_U_01`](#mistw-proc-logc001-u-01) | 0 | 운영 snapshot 원문 우선 |
| [`proc_WLOC004_P_03`](#mistw-proc-wloc004-p-03) | 0 | be_anasa develop 원문 |
| [`proc_WLOC004_P_04`](#mistw-proc-wloc004-p-04) | 0 | be_anasa develop 원문 |
| [`proc_comBasket_A_01`](#mistw-proc-combasket-a-01) | 0 | be_anasa develop 원문 |
| [`proc_GTCE010_A_01`](#mistw-proc-gtce010-a-01) | 0 | be_anasa develop 원문 |
| [`proc_GTCI005_S_00`](#mistw-proc-gtci005-s-00) | 0 | be_anasa develop 원문 |
| [`proc_PURC001_IU_01`](#mistw-proc-purc001-iu-01) | 0 | be_anasa develop 원문 |
| [`proc_PURC001_S_01`](#mistw-proc-purc001-s-01) | 0 | be_anasa develop 원문 |
| [`proc_PURC001_U_01`](#mistw-proc-purc001-u-01) | 0 | be_anasa develop 원문 |
| [`proc_PURC003_S_01`](#mistw-proc-purc003-s-01) | 0 | be_anasa develop 원문 |
| [`proc_PURC003_S_02`](#mistw-proc-purc003-s-02) | 0 | be_anasa develop 원문 |
| [`proc_PURC009_S_01`](#mistw-proc-purc009-s-01) | 0 | be_anasa develop 원문 |
| [`proc_PURC010_S_01`](#mistw-proc-purc010-s-01) | 0 | be_anasa develop 원문 |
| [`proc_PURC010_S_03`](#mistw-proc-purc010-s-03) | 0 | be_anasa develop 원문 |
| [`proc_DeliveryStatus_S`](#mistw-proc-deliverystatus-s) | 0 | be_anasa develop 원문 |
| [`proc_GTOC010_S_02`](#mistw-proc-gtoc010-s-02) | 0 | be_anasa develop 원문 |

<a id="mistw-proc-pure002-s-01"></a>
## `MISTW.dbo.proc_PURE002_S_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURE002_S_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURE002_S_01.sql

```sql



/********************************************************************************
제    목 : 일별재고현황_제품 조회
프로그램 : 일별재고현황(제품)
등 록 일 : 2018-04-05
등 록 자 : 조종갑
수정일		수정자		내용
-----------------------------------------------------------------------

EXEC proc_PURE002_S_01 '20190101', '20190101','20190111','CP','%','',''
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_PURE002_S_01]
	 @i_TotYmd		varchar(8)		-- 누계기간
	,@i_InOutYmdFr	varchar(8)	    -- 수불기간FR
	,@i_InOutYmdTo	varchar(8)		-- 수불기간To
	,@i_AcctGb		varchar(3)		-- 구분 (CP:완제품,GD:상품,'HP':원부재료)
	,@i_ItemGrpCd	varchar(3)		-- 품목군 구분
	,@i_ItemNm		varchar(50)		-- 품목명
	,@i_Spec		varchar(50)		-- 규격
AS

BEGIN
	declare @w_TotYYMM	varchar(6)		-- 기초기준년월

	SET NOCOUNT ON;

	SET @w_TotYYMM = SUBSTRING(REPLACE(@i_InOutYmdFr,'-',''),1,6);


/*
	IF @i_AcctGb IN ('CP','GD')
		BEGIN
			WITH CTE_Item ( ItemNo, ItemNm, Spec, Unit ) 
			AS (
					SELECT a.ItemNo , A.ItemNm, A.Spec, A.Unit
					FROM MISPD..PDItemMaster A
					WHERE A.AcctGb = @i_AcctGb 
					  AND ( @i_ItemGrpCd is null or @i_ItemGrpCd  = '%' or  ( @i_ItemGrpCd is not null and @i_ItemGrpCd <> '%' and ISNULL(A.ItemGrpCd,'') =  @i_ItemGrpCd ))  
					  AND ( @i_ItemNm    is null or @i_ItemNm     = ''  or  ( @i_ItemNm is not null and @i_ItemNm       <> ''  and A.ItemNm               LIKE  @i_ItemNm ))     
					  AND ( @i_Spec      is null or @i_Spec       = ''  or  ( @i_Spec   is not null and @i_Spec         <> ''  and A.Spec                 LIKE  '%'+  @i_Spec +'%'))  
					  AND ( @i_AcctGb    is null or @i_AcctGb     = '%' or  ( @i_AcctGb is not null and @i_AcctGb       <> '%' and ISNULL(A.AcctGb,'')    =  @i_AcctGb ))  
			   )
			SELECT AA.ITEMNO		-- 품번
				 , AA.ITEMNM		-- 품명
				 , AA.SPEC			-- 규격				 
				 , CASE WHEN AA.UNIT ='本' THEN 'M' ELSE AA.UNIT END			UNIT			-- 단위
				 , ISNULL(BB.StdStkQty,0) AS STDSTKQTY		-- 기초재고
				 , ISNULL(BB.InQty,0) AS INQTY				-- 입고
				 , ISNULL(BB.OutQty,0) AS OUTQTY			-- 출고
				 , ISNULL(BB.EndStkQty,0) AS ENDSTKQTY		-- 기말재고
			FROM  CTE_Item AA
				LEFT JOIN (
								SELECT X.ItemNo
									 , SUM(X.StdStkQty) AS StdStkQty
									 , SUM(X.InQty) AS InQty
									 , SUM(X.OutQty) AS OutQty
									 , SUM(X.StdStkQty) + SUM(X.InQty) - SUM(X.OutQty) AS EndStkQty 
								FROM (
										-- 기초재고
										SELECT A.ItemNo
											 , B.BasicStkQty as StdStkQty
											 , 0 as InQty
											 , 0 as OutQty
										FROM CTE_Item A
											INNER JOIN MISTW..tbLogMonStk B
												ON A.ItemNo = B.ItemNo
										WHERE B.StkYm = @w_TotYYMM

										-- 입고
										UNION ALL
										SELECT A.ItemNo
											 , 0 as StdStkQty
											 , SUM(B.inOutQty) as InQty
											 , 0 as OutQty
										FROM CTE_Item A
											INNER JOIN MISTW..tbLogStk B
												ON A.ItemNo = B.ItemNo
										WHERE B.InOutYmd BETWEEN @i_InOutYmdFr AND @i_InOutYmdTo 
										AND B.InOutGb IN ('01','03','07','08')
										GROUP BY A.ItemNo

										-- 출고
										UNION ALL
										SELECT A.ItemNo
											 , 0 as StdStkQty
											 , 0 as InQty
											 , SUM(B.inOutQty) as OutQty
										FROM CTE_Item A
											INNER JOIN MISTW..tbLogStk B
												ON A.ItemNo = B.ItemNo
										WHERE B.InOutYmd BETWEEN @i_InOutYmdFr AND @i_InOutYmdTo 
										AND B.InOutGb IN ('02','04','05','06')
										GROUP BY A.ItemNo
									) X
								GROUP BY X.ItemNo
							 ) BB
				ON AA.ItemNo = BB.ItemNo
			WHERE ( ISNULL(BB.StdStkQty,0) <> 0 OR ISNULL(BB.InQty,0) <> 0 OR ISNULL(BB.OutQty,0) <> 0 OR ISNULL(BB.EndStkQty,0) <> 0 )
			ORDER BY 1,2
		END
*/


	IF @i_AcctGb IN ('CP','GD')
	BEGIN
		WITH CTE_Item ( ItemNo, ItemNm, Spec, Unit, Hyeopga ) 
		AS (SELECT a.ItemNo , A.ItemNm, A.Spec, A.Unit, A.Hyeopga
			FROM   MISPD..PDItemMaster A
			WHERE  A.AcctGb = @i_AcctGb 
			AND  ( @i_ItemGrpCd IS NULL OR @i_ItemGrpCd  = '%' OR  ( @i_ItemGrpCd IS NOT NULL AND @i_ItemGrpCd <> '%' AND ISNULL(A.ItemGrpCd,'') =  @i_ItemGrpCd ))  
			AND  ( @i_ItemNm    IS NULL OR @i_ItemNm     = ''  OR  ( @i_ItemNm IS NOT NULL AND @i_ItemNm       <> ''  AND A.ItemNm               LIKE  @i_ItemNm ))     
			AND  ( @i_Spec      IS NULL OR @i_Spec       = ''  OR  ( @i_Spec   IS NOT NULL AND @i_Spec         <> ''  AND A.Spec                 LIKE  '%'+  @i_Spec +'%'))  
			AND  ( @i_AcctGb    IS NULL OR @i_AcctGb     = '%' OR  ( @i_AcctGb IS NOT NULL AND @i_AcctGb       <> '%' AND ISNULL(A.AcctGb,'')    =  @i_AcctGb ))  
		   )
		SELECT AA.ITEMNO		-- 품번
			 , AA.ITEMNM		-- 품명
			 , AA.SPEC			-- 규격				 
			 , CASE WHEN AA.UNIT ='本' THEN 'M' ELSE AA.UNIT END			UNIT			-- 단위
			 , ISNULL(BB.StdStkQty,0) AS STDSTKQTY		-- 기초재고
			 , ISNULL(BB.InQty,0) AS INQTY				-- 입고
			 , ISNULL(BB.OutQty,0) AS OUTQTY			-- 출고
			 , ISNULL(BB.EndStkQty,0) AS ENDSTKQTY		-- 기말재고
			 , ISNULL(BB.StdStkQty, 0) * ISNULL(AA.Hyeopga, 0) AS STDSTKAMT			-- 기초재고금액
			 , ISNULL(BB.EndStkQty, 0) * ISNULL(AA.Hyeopga, 0) AS ENDSTKAMT			-- 기말재고금액
		FROM   CTE_Item AA
		LEFT JOIN (SELECT t.ItemNo
						, SUM(t.BasicStkQty) StdStkQty
						, SUM(t.InQty) InQty
						, SUM(t.OutQty) + SUM(t.StkAdjQty) OutQty
						, SUM(t.BasicStkQty) + SUM(t.InQty) - SUM(t.OutQty) - SUM(t.StkAdjQty) EndStkQty
				   FROM  (SELECT a.ItemNo, b.BasicStkQty, 0 InQty, 0 OutQty, 0 StkAdjQty, b.EndStkQty
						  FROM   CTE_Item a
						  INNER JOIN MISTW.dbo.tbLogMonStk b ON b.ItemNo = a.ItemNo
						  WHERE  StkYm = @w_TotYYMM
						  UNION ALL 
						  SELECT a.ItemNo
							   , SUM(CASE WHEN b.InOutYmd < @i_InOutYmdFr
										  THEN CASE WHEN b.InOutGb IN ('01','03','07','08') THEN b.InOutQty
													WHEN b.InOutGb IN ('02','04','05','06') THEN -b.InOutQty END
										  ELSE 0 END)
							   , SUM(CASE WHEN b.InOutYmd >= @i_InOutYmdFr
										  THEN CASE WHEN b.InOutGb IN ('01','03','07','08') THEN b.InOutQty ELSE 0 END
										  ELSE 0 END)
							   , SUM(CASE WHEN b.InOutYmd >= @i_InOutYmdFr
										  THEN CASE WHEN b.InOutGb IN ('02','04','05','06') THEN b.InOutQty ELSE 0 END
										  ELSE 0 END)
							   , 0
							   , 0
						  FROM   CTE_Item a
						  INNER JOIN MISTW.dbo.tbLogStk b ON b.ItemNo = a.ItemNo
						  WHERE  b.InOutYmd BETWEEN @w_TotYYMM + '01' AND @i_InOutYmdTo
						  GROUP BY a.ItemNo
						  UNION ALL
						  -- 2019년 6월 이후 재고 조정 등록 내역
						  SELECT a.ItemNo
							   , SUM(CASE WHEN c.StkAdjYmd < @i_InOutYmdFr THEN -b.DiffQty ELSE 0 END)
							   , 0
							   , 0
							   , SUM(CASE WHEN c.StkAdjYmd >= @i_InOutYmdFr THEN b.DiffQty ELSE 0 END)
							   ,0
						  FROM   CTE_Item a
						  INNER JOIN MISTW.dbo.tbLogStkDtl b ON b.ItemNo = a.ItemNo
						  INNER JOIN MISTW.dbo.tbLogStkAdj c ON c.StkAdjNo = b.StkAdjNo
						  WHERE  c.StkAdjYmd BETWEEN @w_TotYYMM + '01' AND @i_InOutYmdTo
						  --AND    c.StkAdjYmd >= '20190601'
						  GROUP BY a.ItemNo
						  --20191122 / 2019년 상반기 재고조정자료 정리로 인해 불필요해짐
						  --UNION ALL
						  -- 2019년 5월 이전 재고 조정 내역
						  --SELECT a.ItemNo
							 --  , SUM(CASE WHEN c.StkAdjYmd < @i_InOutYmdFr THEN -b.DiffQty ELSE 0 END)
							 --  , 0
							 --  , 0
							 --  , SUM(CASE WHEN c.StkAdjYmd >= @i_InOutYmdFr THEN b.DiffQty ELSE 0 END)
							 --  ,0
						  --FROM   CTE_Item a
						  --INNER JOIN MISTW.dbo.tbLogStkDtlHis b ON b.ItemNo = a.ItemNo
						  --INNER JOIN MISTW.dbo.tbLogStkAdj c ON c.StkAdjNo = b.StkAdjNo
						  --WHERE  c.StkAdjYmd BETWEEN @w_TotYYMM + '01' AND @i_InOutYmdTo
						  --AND    c.StkAdjYmd < '20190601'
						  --GROUP BY a.ItemNo
						  ) t
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
		
	/*	
		SELECT t.ItemNo
						, SUM(t.BasicStkQty) StdStkQty
						, SUM(t.InQty) InQty
						, SUM(t.OutQty) OutQty
						, SUM(t.BasicStkQty) + SUM(t.InQty) - SUM(t.OutQty) EndStkQty
				   FROM  (-- 기초재고
					      SELECT x.ItemNo, x.BasicStkQty, 0 InQty, 0 OutQty
					      FROM   MISTW..tbLogMonStk x
					      WHERE  x.StkYm = @w_TotYYMM
					      UNION ALL
					      -- 기초재고(입고)
					      SELECT x.ItemNo, SUM(x.InOutQty), 0, 0
					      FROM   MISTW.dbo.tbLogStk x
					      WHERE  x.InOutYmd >= @w_TotYYMM + '01'
					      AND    x.InOutYmd < @i_InOutYmdFr
					      AND    x.InOutGb IN ('01','03','07','08')
					      GROUP BY x.ItemNo
					      UNION ALL
					      -- 기초재고(출고)
					      SELECT x.ItemNo, -SUM(x.InOutQty), 0, 0
					      FROM   MISTW.dbo.tbLogStk x
					      WHERE  x.InOutYmd >= @w_TotYYMM + '01'
					      AND    x.InOutYmd < @i_InOutYmdFr
					      AND    x.InOutGb IN ('02','04','05','06')
					      GROUP BY x.ItemNo
					      UNION ALL
					      -- 입고
					      SELECT x.ItemNo, 0, SUM(x.InOutQty), 0
					      FROM   MISTW.dbo.tbLogStk x
					      WHERE  x.InOutYmd BETWEEN @i_InOutYmdFr AND @i_InOutYmdTo
					      AND    x.InOutGb IN ('01','03','07','08')
					      GROUP BY x.ItemNo
					      UNION ALL
					      -- 출고
					      SELECT x.ItemNo, 0, 0, SUM(x.InOutQty)
					      FROM   MISTW.dbo.tbLogStk x
					      WHERE  x.InOutYmd BETWEEN @i_InOutYmdFr AND @i_InOutYmdTo
					      AND    x.InOutGb IN ('02','04','05','06')
					      GROUP BY x.ItemNo) t*/
				   GROUP BY t.ItemNo) BB ON BB.ItemNo = AA.ItemNo
		WHERE  ISNULL(BB.StdStkQty, 0) <> 0 OR ISNULL(BB.InQty, 0) <> 0 OR ISNULL(BB.OutQty, 0) <> 0 OR ISNULL(BB.EndStkQty, 0) <> 0
		ORDER BY 1, 2
	END


END

RETURN
```

<a id="mistw-proc-pure002-s-02"></a>
## `MISTW.dbo.proc_PURE002_S_02`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURE002_S_02.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURE002_S_02.sql

```sql



/********************************************************************************
제    목 : 일별재고현황_재료 조회
프로그램 : 일별재고현황(재료)
등 록 일 : 2018-04-04
등 록 자 : 조종갑
수정일		수정자		내용
-----------------------------------------------------------------------
20230727	박병주		Query 속도 향상으로 위해 동적 JOIN을 temp_table로 처리

EXEC proc_PURE002_S_02 '20190501', '20190531', '%','','', ''
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_PURE002_S_02]
	 @I_INYMDFR_S   varchar(8)		-- 입고년월
    ,@I_INYMDTO_S   varchar(8)      
	,@i_ItemGrpCd	varchar(3)		-- 품목군 구분
	,@i_ItemNo		varchar(50)		-- 품목코드
	,@i_CustCd		varchar(5)		-- 거래처코드
	,@i_TabGb		varchar(5) = ''	-- 화면탭해당계정


AS

BEGIN

	SET NOCOUNT ON;
/*
	SELECT MISTW.DBO.fn_comCodeNm('PDA049', AA.ItemGrpCd, '1') AS ITEMGRPNM -- 품목군
		 , AA.ITEMNO		-- 품번
		 , AA.ITEMNM		-- 품명
		 , AA.SPEC			-- 규격		 
		 , CASE WHEN AA.UNIT ='本' THEN 'M' ELSE AA.UNIT END			UNIT			-- 단위
		 , AA.PROCESSNM		-- 공정명
		 , MISTW.DBO.fn_comCodeNm('PUR001', AA.AcctGb, '1') AS ACCTGB -- 구분
		 , ISNULL(BB.BasicQty,0) AS BASICQTY	-- 기초재고
		 , AA.INQTY								-- 입고수량
		 , ISNULL(BB.StockQty,0) AS STOCKQTY	-- 현재고
		 , ISNULL(BB.BasicQty,0) + AA.InQty - ISNULL(BB.StockQty,0) AS USEQTY	-- 사용량
	FROM (
			SELECT c.ItemGrpCd
				 , B.ItemNo		-- 품번
				 , C.ItemNm		-- 품명
				 , C.Spec		-- 규격
				 , C.Unit		-- 단위
				 , b.ProcessCd
				 , D.ProcessNm	-- 공정명
				 , D.AcctGb
				 , SUM(B.InQty) AS InQty		-- 수량
			FROM MISTW..tbPurIn A
				INNER JOIN MISTW..tbPurInDtl B
					ON A.InMgmNo = B.InMgmNo
				INNER JOIN MISPD..PDItemMaster C
					ON B.ItemNo = C.ItemNo
				INNER JOIN MISPD..PDItemProcess D
					ON B.ItemNo = D.ItemNo
					AND B.ProcessCd =D.ProcessCd
			--WHERE A.InYmd like @i_InYYMM + '%'
			WHERE A.InYmd between @I_INYMDFR_S and @I_INYMDTO_S
			AND ( @i_CustCd     is null or @i_CustCd     = '' or  ( @i_CustCd      is not null and @i_CustCd       <> '' and A.CustCd                 =  @i_CustCd ))  
			AND ( @i_ItemNo     is null or @i_ItemNo     = '' or  ( @i_ItemNo      is not null and @i_ItemNo       <> '' and B.ItemNo                 =  @i_ItemNo ))     
			AND ( @i_ItemGrpCd  is null or @i_ItemGrpCd  = '%' or  ( @i_ItemGrpCd   is not null and @i_ItemGrpCd    <> '%' and isnull(c.ItemGrpCd,'')   =  @i_ItemGrpCd ))  
			GROUP BY c.ItemGrpCd
				  , B.ItemNo		
				  , C.ItemNm		
				  , C.Spec		
				  , C.Unit	
				  , b.ProcessCd	
				  , D.ProcessNm	
				  , D.AcctGb
		) AA
		LEFT JOIN MISPD..PDPlanProcessDetail BB
			ON AA.ItemNo = BB.ItemNo
			AND AA.ProcessCd = BB.ProcessCd
			AND BB.PlanYm = CONVERT (varchar(6) ,@I_INYMDFR_S)
	ORDER BY 1,2,3
*/

--2023.07.27 #1 aa table temp table로 생성
	SELECT	b.ItemNo, b.ProcessCd, SUM(b.InQty) InQty
   	INTO	#tempAA
	FROM	MISTW.dbo.tbPurIn a
	INNER JOIN MISTW.dbo.tbPurInDtl b ON b.InMgmNo = a.InMgmNo
	WHERE	a.InYmd BETWEEN @I_INYMDFR_S AND @I_INYMDTO_S
	GROUP BY b.ItemNo, b.ProcessCd
--2023.07.27 #1 수정종료


	SELECT MISTW.dbo.fn_comCodeNm('PDA049', c.ItemGrpCd, '1')	ITEMGRPNM		-- 품목군
		 , bb.ItemNo		ITEMNO		-- 품번
		 , c.ItemNm			ITEMNM		-- 품명
		 , c.Spec			SPEC		-- 규격
		 , CASE WHEN c.Unit = '本' THEN 'M' ELSE c.Unit END		UNIT			-- 단위
		 , d.ProcessNm		PROCESSNM	-- 공정명
		 , MISTW.dbo.fn_comCodeNm('PUR001', d.AcctGb, '1')		ACCTGB			-- 구분
		 --, ISNULL(bb.BasicQty, 0)		BASICQTY		-- 기초재고
		 --, ISNULL(aa.InQty, 0)			INQTY			-- 입고수량
		 --, ISNULL(bb.StockQty, 0)		STOCKQTY		-- 현재고
		 --, ISNULL(bb.BasicQty, 0) + ISNULL(aa.InQty, 0) - ISNULL(bb.StockQty, 0)	USEQTY	-- 사용량
		 --, ISNULL(bb.BasicQty, 0) * ISNULL(e.Price, 0)			BASICAMT		-- 기초재고금액
		 --, ISNULL(bb.StockQty, 0) * ISNULL(e.Price, 0)			STOCKAMT		-- 기말재고금액
		 -- 20190712 / 기간선택 가능하게 변경
		 , SUM(CASE WHEN bb.PlanYm = LEFT(@I_INYMDFR_S, 6) THEN ISNULL(bb.BasicQty, 0) ELSE 0 END / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END)		BASICQTY		-- 기초재고
		 , SUM(ISNULL(aa.InQty, 0) / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END)			INQTY			-- 입고수량
		 , SUM(CASE WHEN bb.PlanYm = LEFT(@I_INYMDTO_S, 6) THEN ISNULL(bb.StockQty, 0) ELSE 0 END / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END)		STOCKQTY		-- 현재고
		 , SUM(CASE WHEN bb.PlanYm = LEFT(@I_INYMDFR_S, 6) THEN ISNULL(bb.BasicQty, 0) ELSE 0 END
			 + ISNULL(aa.InQty, 0)
			 - CASE WHEN bb.PlanYm = LEFT(@I_INYMDTO_S, 6) THEN ISNULL(bb.StockQty, 0) ELSE 0 END
		 / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END)			USEQTY	-- 사용량
		 , SUM(CASE WHEN bb.PlanYm = LEFT(@I_INYMDFR_S, 6) THEN ISNULL(bb.BasicQty, 0) ELSE 0 END / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END * ISNULL(e.Price, 0))		BASICAMT		-- 기초재고금액
		 , SUM(CASE WHEN bb.PlanYm = LEFT(@I_INYMDTO_S, 6) THEN ISNULL(bb.StockQty, 0) ELSE 0 END / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END * ISNULL(e.Price, 0))		STOCKAMT		-- 기말재고금액
		 --, ISNULL(bb.BasicQty, 0) / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END		BASICQTY		-- 기초재고
		 --, ISNULL(aa.InQty, 0) / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END			INQTY			-- 입고수량
		 --, ISNULL(bb.StockQty, 0) / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END		STOCKQTY		-- 현재고
		 --, (ISNULL(bb.BasicQty, 0) + ISNULL(aa.InQty, 0) - ISNULL(bb.StockQty, 0)) / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END	USEQTY	-- 사용량
		 --, (ISNULL(bb.BasicQty, 0) / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END) * ISNULL(e.Price, 0)		BASICAMT		-- 기초재고금액
		 --, (ISNULL(bb.StockQty, 0) / CASE WHEN c.Unit = '本' THEN 6000 ELSE 1 END) * ISNULL(e.Price, 0)		STOCKAMT		-- 기말재고금액
	FROM   MISPD.dbo.PDPlanProcessDetail bb
-- 2023.07.27 #2 aa table temp table로 생성
--	LEFT JOIN (SELECT b.ItemNo, b.ProcessCd, SUM(b.InQty) InQty
--			   FROM   MISTW.dbo.tbPurIn a
--			   INNER JOIN MISTW.dbo.tbPurInDtl b ON b.InMgmNo = a.InMgmNo
--			   WHERE  a.InYmd BETWEEN @I_INYMDFR_S AND @I_INYMDTO_S
--			   --AND  ( @i_CustCd IS NULL OR @i_CustCd = '' OR (@i_CustCd IS NOT NULL AND @i_CustCd <> '' AND a.CustCd = @i_CustCd) )
--			   GROUP BY b.ItemNo, b.ProcessCd) aa ON aa.ItemNo = bb.ItemNo AND aa.ProcessCd = bb.ProcessCd
	LEFT JOIN #tempAA aa ON aa.ItemNo = bb.ItemNo AND aa.ProcessCd = bb.ProcessCd
-- 2023.07.27 #2 수정종료
	INNER JOIN MISPD.dbo.PDItemMaster c ON c.ItemNo = bb.ItemNo
	INNER JOIN MISPD.dbo.PDItemProcess d ON d.ItemNo = bb.ItemNo AND d.ProcessCd = bb.ProcessCd
	INNER JOIN (SELECT ItemNo, ProcessCd, AVG(Price) Price 
				FROM MISPD.dbo.tbProcessPrice x
				WHERE  ChgYmd = (SELECT MAX(y.ChgYmd)
								 FROM   MISPD.dbo.tbProcessPrice y
								 WHERE  y.ItemNo = x.ItemNo
								 AND    y.ProcessCd = x.ProcessCd
								 AND    y.CustCd = x.CustCd
								 AND    y.ChgYmd <= @I_INYMDTO_S) 
				GROUP BY ItemNo, ProcessCd) e ON e.ItemNo = bb.ItemNo AND e.ProcessCd = bb.ProcessCd
	WHERE  bb.PlanYm BETWEEN LEFT(@I_INYMDFR_S, 6) AND LEFT(@I_INYMDTO_S, 6)
	AND    d.AcctGb IN ('001', '002')
	AND  ( @i_ItemNo IS NULL OR @i_ItemNo = '' OR (@i_ItemNo IS NOT NULL AND @i_ItemNo <> '' AND bb.ItemNo = @i_ItemNo) )
	AND  ( @i_ItemGrpCd IS NULL OR @i_ItemGrpCd = '%' OR (@i_ItemGrpCd IS NOT NULL AND @i_ItemGrpCd <> '%' AND ISNULL(c.ItemGrpCd, '') = @i_ItemGrpCd) )
	GROUP BY c.ItemGrpCd
		   , bb.ItemNo
		   , c.ItemNm
		   , c.Spec
		   , CASE WHEN c.Unit = '本' THEN 'M' ELSE c.Unit END
		   , d.ProcessNm
		   , d.AcctGb
	ORDER BY 1, 2, 3




END

RETURN
```

<a id="mistw-proc-logb005-s-03-v2"></a>
## `MISTW.dbo.proc_LOGB005_S_03_V2`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **운영 snapshot 원문 우선**
원본 경로: `/Users/cigro/Desktop/anasa/anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/1321107797_proc_LOGB005_S_03_V2.sql`
운영 snapshot은 저장소에 포함되지 않은 읽기 전용 스냅샷이며, 이 문서에는 해당 snapshot 파일을 그대로 보관한다.

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

<a id="mistw-proc-logb009-s-gp-ord-v2"></a>
## `MISTW.dbo.proc_LOGB009_S_GP_ORD_V2`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **운영 snapshot 원문 우선**
원본 경로: `/Users/cigro/Desktop/anasa/anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/1225107455_proc_LOGB009_S_GP_ORD_V2.sql`
운영 snapshot은 저장소에 포함되지 않은 읽기 전용 스냅샷이며, 이 문서에는 해당 snapshot 파일을 그대로 보관한다.

```sql
-- Database: MISTW
-- Object: [dbo].[proc_LOGB009_S_GP_ORD_V2]
-- Type: SQL_STORED_PROCEDURE
SET ANSI_NULLS ON;
GO
SET QUOTED_IDENTIFIER ON;
GO
/********************************************************************************
Program: LOG-GP-003M GP unpaid packing order inquiry V2
Ticket: ANA-85
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_LOGB009_S_GP_ORD_V2]
      @iDeliveryReqYmdF varchar(10) = ''
    , @iDeliveryReqYmdT varchar(10) = ''
    , @iCustCd          varchar(50) = ''
    , @iEquipCustNm     varchar(100) = ''
    , @iSite            varchar(100) = ''
    , @iStockYn         varchar(1) = ''
AS
BEGIN
    SET NOCOUNT ON;

    SELECT ItemNo, ItemNm, Spec, Unit
    INTO #tmpItem
    FROM MISPD.dbo.PDItemMaster WITH (NOLOCK);

    SELECT
          a.CustCd CUSTCD
        , dbo.fn_comCustNm(a.CustCd) CUSTNM
        , a.EquipCustNm EQUIPCUSTNM
        , a.Site SITE
        , a.Adr ADR
        , a.OrdRmk ORDRMK
        , b.LineMemo MEMO
        , b.ItemNo ITEMNO
        , c.ItemNm ITEMNM
        , c.Spec SPEC
        , c.Unit UNIT
        , b.OrdQty ORDQTY
        , ISNULL(d.PkgQty, 0) PKGQTY
        , b.OrdQty - ISNULL(d.PkgQty, 0) DEFAULTQTY
        , dbo.fn_logEndStkQty(1, b.ItemNo) STOCKQTY
        , a.OrdNo ORDNO
        , a.OrdDte ORDDTE
        , MISCM.dbo.fn_gfnDateTypeTrans(a.DeliveryReqYmd, '-') DELIVERYREQYMD
        , '' SHIPMENTYMD
        , DATEDIFF(d, CONVERT(datetime, a.DeliveryReqYmd, 112), GETDATE()) ELAPSEDDAYS
        , b.OrdEnrDtlNo ORDENRDTLNO
        , a.OrdEnrNo ORDENRNO
        , a.ItemGrpCd ITEMGRPCD
    INTO #Temp
    FROM MISTW.dbo.tbLogOrdEnr a WITH (NOLOCK)
    INNER JOIN MISTW.dbo.tbLogOrdEnrDtl b WITH (NOLOCK)
        ON b.OrdEnrNo = a.OrdEnrNo
    INNER JOIN #tmpItem c
        ON c.ItemNo = b.ItemNo
    LEFT JOIN (
        SELECT x.OrdEnrDtlNo, SUM(ISNULL(y.OutQty, x.PkgQty)) PkgQty
        FROM MISTW.dbo.tbLogPkgEnrDtl x WITH (NOLOCK)
        LEFT JOIN MISTW.dbo.tbLogOutDtl y WITH (NOLOCK)
            ON y.PkgEnrDtlNo = x.PkgEnrDtlNo
        GROUP BY x.OrdEnrDtlNo
    ) d
        ON d.OrdEnrDtlNo = b.OrdEnrDtlNo
    WHERE (@iDeliveryReqYmdF = '' OR a.DeliveryReqYmd >= REPLACE(@iDeliveryReqYmdF, '-', ''))
      AND (@iDeliveryReqYmdT = '' OR a.DeliveryReqYmd <= REPLACE(@iDeliveryReqYmdT, '-', ''))
      AND ISNULL(b.OutStat, '') NOT IN ('04', '99')
      AND a.OrdType NOT IN ('02', '03')
      AND ISNULL(b.OrdItemRecYn, '') = 'Y'
      AND ISNULL(b.CloseYn, '') <> 'Y'
      AND b.OrdQty - ISNULL(d.PkgQty, 0) <> 0
      AND a.ItemGrpCd = 'GP'
      AND (@iCustCd = '' OR a.CustCd = @iCustCd)
      AND (@iEquipCustNm = '' OR a.EquipCustNm LIKE '%'+ @iEquipCustNm +'%')
      AND (@iSite = '' OR a.Site LIKE '%'+ @iSite +'%');

    IF @iStockYn = 'Y'
        DELETE FROM #Temp WHERE STOCKQTY <= 0;

    SELECT *
    FROM #Temp
    ORDER BY ITEMNM, SPEC, DELIVERYREQYMD;
END
GO
```

<a id="mistw-proc-loge002-s-01"></a>
## `MISTW.dbo.proc_LOGE002_S_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **운영 snapshot 원문 우선**
원본 경로: `/Users/cigro/Desktop/anasa/anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/1166627199_proc_LOGE002_S_01.sql`
운영 snapshot은 저장소에 포함되지 않은 읽기 전용 스냅샷이며, 이 문서에는 해당 snapshot 파일을 그대로 보관한다.

```sql
-- Database: MISTW
-- Object: [dbo].[proc_LOGE002_S_01]
-- Type: SQL_STORED_PROCEDURE
SET ANSI_NULLS ON;
GO
SET QUOTED_IDENTIFIER ON;
GO
CREATE PROCEDURE [dbo].[proc_LOGE002_S_01]
     @i_OrdDteFr        varchar(8)
    ,@i_OrdDteTo        varchar(8)
    ,@i_ItemGrpCd       varchar(3)
    ,@i_CustNo          varchar(10)
    ,@i_EquipCustNm     varchar(100)
    ,@i_Site            varchar(100)
    ,@i_DeliveryPlace   varchar(100)
    ,@i_ItemNo          varchar(50)
    ,@i_Page            int = 1
    ,@i_Limit           int = 2147483647
    ,@i_SortBy          varchar(50) = ''
    ,@i_SortOrder       varchar(4) = 'ASC'
AS
BEGIN
    SET NOCOUNT ON;
    SET @i_Page = CASE WHEN ISNULL(@i_Page, 0) < 1 THEN 1 ELSE @i_Page END;
    SET @i_Limit = CASE WHEN ISNULL(@i_Limit, 0) < 1 THEN 2147483647 ELSE @i_Limit END;
    SET @i_SortBy = UPPER(ISNULL(@i_SortBy, ''));
    SET @i_SortOrder = CASE WHEN UPPER(ISNULL(@i_SortOrder, 'ASC')) = 'DESC'
                            THEN 'DESC' ELSE 'ASC' END;

    SELECT
         B.OrdEnrDtlNo
        ,MAX(D.CustNm) AS CUSTNM
        ,ISNULL(A.EquipCustNm, '') AS EQUIPCUSTNM
        ,ISNULL(A.Site, '') AS SITE
        ,A.CustCd AS CUSTCD
        ,CONVERT(decimal(38, 6), ISNULL(B.OrdQty, 0)) AS ORDQTY
    INTO #Eligible
    FROM MISTW.dbo.tbLogOrdEnr AS A
    INNER JOIN MISTW.dbo.tbLogOrdEnrDtl AS B ON A.OrdEnrNo = B.OrdEnrNo
    INNER JOIN MISPD.dbo.PDItemMaster AS C ON B.ItemNo = C.ItemNo
    INNER JOIN MISSA.dbo.SACust AS D ON A.CustCd = D.CustCd
    WHERE A.DeliveryReqYmd BETWEEN @i_OrdDteFr AND @i_OrdDteTo
      AND ISNULL(B.OutStat, '') NOT IN ('04', '99')
      AND A.OrdType NOT IN ('02', '03')
      AND ISNULL(B.OrdItemRecYn, '') = 'Y'
      AND ISNULL(B.CloseYn, '') <> 'Y'
      AND (@i_ItemGrpCd IS NULL OR @i_ItemGrpCd IN ('', '%') OR C.ItemGrpCd = @i_ItemGrpCd)
      AND (@i_CustNo IS NULL OR @i_CustNo = '' OR A.CustCd = @i_CustNo)
      AND (@i_EquipCustNm IS NULL OR @i_EquipCustNm = '' OR ISNULL(A.EquipCustNm, '') LIKE '%' + @i_EquipCustNm + '%')
      AND (@i_Site IS NULL OR @i_Site = '' OR ISNULL(A.Site, '') LIKE '%' + @i_Site + '%')
      AND (@i_DeliveryPlace IS NULL OR @i_DeliveryPlace = '' OR ISNULL(A.DeliveryPlace, '') LIKE '%' + @i_DeliveryPlace + '%')
      AND (@i_ItemNo IS NULL OR @i_ItemNo = '' OR B.ItemNo = @i_ItemNo)
    GROUP BY B.OrdEnrDtlNo, ISNULL(A.EquipCustNm, ''), ISNULL(A.Site, ''), A.CustCd, B.OrdQty;

    CREATE CLUSTERED INDEX IX_LOGE002_S01_Eligible_Dtl ON #Eligible (OrdEnrDtlNo);

    SELECT y.OrdEnrDtlNo, SUM(ISNULL(y.OutQty, 0)) AS OutQty
    INTO #ActualOut
    FROM MISTW.dbo.tbLogOutDtl AS y
    INNER JOIN #Eligible AS eligible ON eligible.OrdEnrDtlNo = y.OrdEnrDtlNo
    GROUP BY y.OrdEnrDtlNo;

    SELECT
         eligible.CUSTNM, eligible.EQUIPCUSTNM, eligible.SITE, eligible.CUSTCD
        ,SUM(eligible.ORDQTY) AS ORDQTY
        ,SUM(CONVERT(decimal(38, 6), ISNULL(actual_out.OutQty, 0))) AS OUTQTY
        ,SUM(CONVERT(decimal(38, 6), eligible.ORDQTY - ISNULL(actual_out.OutQty, 0))) AS NOOUTQTY
    INTO #Result
    FROM #Eligible AS eligible
    LEFT JOIN #ActualOut AS actual_out ON actual_out.OrdEnrDtlNo = eligible.OrdEnrDtlNo
    WHERE eligible.ORDQTY - ISNULL(actual_out.OutQty, 0) <> 0
    GROUP BY eligible.CUSTNM, eligible.EQUIPCUSTNM, eligible.SITE, eligible.CUSTCD;

    SELECT CUSTNM, EQUIPCUSTNM, SITE, CUSTCD, ORDQTY, OUTQTY, NOOUTQTY
    FROM #Result
    ORDER BY
         CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'CUSTNM' THEN CUSTNM END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'EQUIPCUSTNM' THEN EQUIPCUSTNM END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'SITE' THEN SITE END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'CUSTCD' THEN CUSTCD END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'ORDQTY' THEN ORDQTY END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'OUTQTY' THEN OUTQTY END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'NOOUTQTY' THEN NOOUTQTY END ASC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'CUSTNM' THEN CUSTNM END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'EQUIPCUSTNM' THEN EQUIPCUSTNM END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'SITE' THEN SITE END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'CUSTCD' THEN CUSTCD END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'ORDQTY' THEN ORDQTY END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'OUTQTY' THEN OUTQTY END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'NOOUTQTY' THEN NOOUTQTY END DESC
        ,CUSTNM ASC, EQUIPCUSTNM ASC, SITE ASC, CUSTCD ASC
    OFFSET (@i_Page - 1) * @i_Limit ROWS FETCH NEXT @i_Limit ROWS ONLY;

    SELECT COUNT_BIG(*) AS TOTAL_COUNT
        ,SUM(ORDQTY) AS ORDQTY_SUM, AVG(ORDQTY) AS ORDQTY_AVG, MIN(ORDQTY) AS ORDQTY_MIN, MAX(ORDQTY) AS ORDQTY_MAX
        ,SUM(OUTQTY) AS OUTQTY_SUM, AVG(OUTQTY) AS OUTQTY_AVG, MIN(OUTQTY) AS OUTQTY_MIN, MAX(OUTQTY) AS OUTQTY_MAX
        ,SUM(NOOUTQTY) AS NOOUTQTY_SUM, AVG(NOOUTQTY) AS NOOUTQTY_AVG, MIN(NOOUTQTY) AS NOOUTQTY_MIN, MAX(NOOUTQTY) AS NOOUTQTY_MAX
    FROM #Result;
END;
GO
```

<a id="mistw-proc-loge002-s-02"></a>
## `MISTW.dbo.proc_LOGE002_S_02`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **운영 snapshot 원문 우선**
원본 경로: `/Users/cigro/Desktop/anasa/anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/1214627370_proc_LOGE002_S_02.sql`
운영 snapshot은 저장소에 포함되지 않은 읽기 전용 스냅샷이며, 이 문서에는 해당 snapshot 파일을 그대로 보관한다.

```sql
-- Database: MISTW
-- Object: [dbo].[proc_LOGE002_S_02]
-- Type: SQL_STORED_PROCEDURE
SET ANSI_NULLS ON;
GO
SET QUOTED_IDENTIFIER ON;
GO
CREATE PROCEDURE [dbo].[proc_LOGE002_S_02]
     @i_OrdDteFr        varchar(8)
    ,@i_OrdDteTo        varchar(8)
    ,@i_ItemGrpCd       varchar(3)
    ,@i_CustNo          varchar(10)
    ,@i_EquipCustNm     varchar(100)
    ,@i_Site            varchar(100)
    ,@i_DeliveryPlace   varchar(100)
    ,@i_ItemNo          varchar(50)
    ,@i_Page            int = 1
    ,@i_Limit           int = 2147483647
    ,@i_SortBy          varchar(50) = ''
    ,@i_SortOrder       varchar(4) = 'ASC'
AS
BEGIN
    SET NOCOUNT ON;

    SET @i_Page = CASE WHEN ISNULL(@i_Page, 0) < 1 THEN 1 ELSE @i_Page END;
    SET @i_Limit = CASE WHEN ISNULL(@i_Limit, 0) < 1 THEN 2147483647 ELSE @i_Limit END;
    SET @i_SortBy = UPPER(ISNULL(@i_SortBy, ''));
    SET @i_SortOrder = CASE WHEN UPPER(ISNULL(@i_SortOrder, 'ASC')) = 'DESC'
                            THEN 'DESC' ELSE 'ASC' END;

    ;WITH ActualOut AS (
        SELECT y.OrdEnrDtlNo, SUM(ISNULL(y.OutQty, 0)) AS OutQty
        FROM MISTW.dbo.tbLogOutDtl AS y
        GROUP BY y.OrdEnrDtlNo
    )
    SELECT
         A.OrdNo AS ORDNO
        ,CASE WHEN B.FPOrdKind = '01' THEN '(S)' + C.ItemNm ELSE C.ItemNm END AS ITEMNM
        ,C.Spec AS SPEC
        ,C.Unit AS UNIT
        ,CONVERT(decimal(38, 6), ISNULL(B.OrdQty, 0)) AS ORDQTY
        ,CONVERT(decimal(38, 6), ISNULL(E.OutQty, 0)) AS OUTQTY
        ,CONVERT(decimal(38, 6), ISNULL(B.OrdQty, 0) - ISNULL(E.OutQty, 0)) AS NOOUTQTY
        ,A.OrdEnrNo AS _ORDENRNO
        ,B.OrdEnrDtlNo AS _ORDENRDTLNO
    INTO #Result
    FROM MISTW.dbo.tbLogOrdEnr AS A
    INNER JOIN MISTW.dbo.tbLogOrdEnrDtl AS B
        ON A.OrdEnrNo = B.OrdEnrNo
    INNER JOIN MISPD.dbo.PDItemMaster AS C
        ON B.ItemNo = C.ItemNo
    INNER JOIN MISSA.dbo.SACust AS D
        ON A.CustCd = D.CustCd
    LEFT JOIN ActualOut AS E
        ON E.OrdEnrDtlNo = B.OrdEnrDtlNo
    WHERE A.DeliveryReqYmd BETWEEN @i_OrdDteFr AND @i_OrdDteTo
      AND ISNULL(B.OutStat, '') NOT IN ('04', '99')
      AND A.OrdType NOT IN ('02', '03')
      AND ISNULL(B.OrdItemRecYn, '') = 'Y'
      AND ISNULL(B.CloseYn, '') <> 'Y'
      AND ISNULL(B.OrdQty, 0) - ISNULL(E.OutQty, 0) <> 0
      AND A.CustCd = ISNULL(@i_CustNo, '')
      AND ISNULL(A.EquipCustNm, '') = ISNULL(@i_EquipCustNm, '')
      AND ISNULL(A.Site, '') = ISNULL(@i_Site, '')
      AND (@i_ItemGrpCd IS NULL OR @i_ItemGrpCd IN ('', '%') OR C.ItemGrpCd = @i_ItemGrpCd)
      AND (@i_DeliveryPlace IS NULL OR @i_DeliveryPlace = ''
           OR ISNULL(A.DeliveryPlace, '') LIKE '%' + @i_DeliveryPlace + '%')
      AND (@i_ItemNo IS NULL OR @i_ItemNo = '' OR B.ItemNo = @i_ItemNo);

    SELECT ORDNO, ITEMNM, SPEC, UNIT, ORDQTY, OUTQTY, NOOUTQTY
    FROM #Result
    ORDER BY
         CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'ORDNO' THEN ORDNO END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'ITEMNM' THEN ITEMNM END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'SPEC' THEN SPEC END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'UNIT' THEN UNIT END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'ORDQTY' THEN ORDQTY END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'OUTQTY' THEN OUTQTY END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'NOOUTQTY' THEN NOOUTQTY END ASC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'ORDNO' THEN ORDNO END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'ITEMNM' THEN ITEMNM END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'SPEC' THEN SPEC END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'UNIT' THEN UNIT END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'ORDQTY' THEN ORDQTY END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'OUTQTY' THEN OUTQTY END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'NOOUTQTY' THEN NOOUTQTY END DESC
        ,ORDNO ASC, _ORDENRNO ASC, _ORDENRDTLNO ASC
    OFFSET (@i_Page - 1) * @i_Limit ROWS
    FETCH NEXT @i_Limit ROWS ONLY;

    SELECT
         COUNT_BIG(*) AS TOTAL_COUNT
        ,SUM(ORDQTY) AS ORDQTY_SUM
        ,AVG(ORDQTY) AS ORDQTY_AVG
        ,MIN(ORDQTY) AS ORDQTY_MIN
        ,MAX(ORDQTY) AS ORDQTY_MAX
        ,SUM(OUTQTY) AS OUTQTY_SUM
        ,AVG(OUTQTY) AS OUTQTY_AVG
        ,MIN(OUTQTY) AS OUTQTY_MIN
        ,MAX(OUTQTY) AS OUTQTY_MAX
        ,SUM(NOOUTQTY) AS NOOUTQTY_SUM
        ,AVG(NOOUTQTY) AS NOOUTQTY_AVG
        ,MIN(NOOUTQTY) AS NOOUTQTY_MIN
        ,MAX(NOOUTQTY) AS NOOUTQTY_MAX
    FROM #Result;
END;
GO
```

<a id="mistw-proc-loge002-s-03"></a>
## `MISTW.dbo.proc_LOGE002_S_03`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **운영 snapshot 원문 우선**
원본 경로: `/Users/cigro/Desktop/anasa/anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/1278627598_proc_LOGE002_S_03.sql`
운영 snapshot은 저장소에 포함되지 않은 읽기 전용 스냅샷이며, 이 문서에는 해당 snapshot 파일을 그대로 보관한다.

```sql
-- Database: MISTW
-- Object: [dbo].[proc_LOGE002_S_03]
-- Type: SQL_STORED_PROCEDURE
SET ANSI_NULLS ON;
GO
SET QUOTED_IDENTIFIER ON;
GO
CREATE PROCEDURE [dbo].[proc_LOGE002_S_03]
     @i_OrdDteFr        varchar(8)
    ,@i_OrdDteTo        varchar(8)
    ,@i_ItemGrpCd       varchar(3)
    ,@i_CustNo          varchar(10)
    ,@i_EquipCustNm     varchar(100)
    ,@i_Site            varchar(100)
    ,@i_DeliveryPlace   varchar(100)
    ,@i_ItemNo          varchar(50)
    ,@i_Page            int = 1
    ,@i_Limit           int = 2147483647
    ,@i_SortBy          varchar(50) = ''
    ,@i_SortOrder       varchar(4) = 'ASC'
AS
BEGIN
    SET NOCOUNT ON;

    SET @i_Page = CASE WHEN ISNULL(@i_Page, 0) < 1 THEN 1 ELSE @i_Page END;
    SET @i_Limit = CASE WHEN ISNULL(@i_Limit, 0) < 1 THEN 2147483647 ELSE @i_Limit END;
    SET @i_SortBy = UPPER(ISNULL(@i_SortBy, ''));
    SET @i_SortOrder = CASE WHEN UPPER(ISNULL(@i_SortOrder, 'ASC')) = 'DESC'
                            THEN 'DESC' ELSE 'ASC' END;

    ;WITH ActualOut AS (
        SELECT y.OrdEnrDtlNo, SUM(ISNULL(y.OutQty, 0)) AS OutQty
        FROM MISTW.dbo.tbLogOutDtl AS y
        GROUP BY y.OrdEnrDtlNo
    )
    SELECT
         B.ItemNo AS ITEMNO
        ,MAX(C.ItemNm) AS ITEMNM
        ,MAX(C.Spec) AS SPEC
        ,SUM(CONVERT(decimal(38, 6), ISNULL(B.OrdQty, 0))) AS ORDQTY
        ,SUM(CONVERT(decimal(38, 6), ISNULL(E.OutQty, 0))) AS OUTQTY
        ,SUM(CONVERT(decimal(38, 6), ISNULL(B.OrdQty, 0) - ISNULL(E.OutQty, 0))) AS NOOUTQTY
    INTO #Result
    FROM MISTW.dbo.tbLogOrdEnr AS A
    INNER JOIN MISTW.dbo.tbLogOrdEnrDtl AS B
        ON A.OrdEnrNo = B.OrdEnrNo
    INNER JOIN MISPD.dbo.PDItemMaster AS C
        ON B.ItemNo = C.ItemNo
    INNER JOIN MISSA.dbo.SACust AS D
        ON A.CustCd = D.CustCd
    LEFT JOIN ActualOut AS E
        ON E.OrdEnrDtlNo = B.OrdEnrDtlNo
    WHERE A.DeliveryReqYmd BETWEEN @i_OrdDteFr AND @i_OrdDteTo
      AND ISNULL(B.OutStat, '') NOT IN ('04', '99')
      AND A.OrdType NOT IN ('02', '03')
      AND ISNULL(B.OrdItemRecYn, '') = 'Y'
      AND ISNULL(B.CloseYn, '') <> 'Y'
      AND ISNULL(B.OrdQty, 0) - ISNULL(E.OutQty, 0) <> 0
      AND (@i_ItemGrpCd IS NULL OR @i_ItemGrpCd IN ('', '%') OR C.ItemGrpCd = @i_ItemGrpCd)
      AND (@i_CustNo IS NULL OR @i_CustNo = '' OR A.CustCd = @i_CustNo)
      AND (@i_EquipCustNm IS NULL OR @i_EquipCustNm = ''
           OR ISNULL(A.EquipCustNm, '') LIKE '%' + @i_EquipCustNm + '%')
      AND (@i_Site IS NULL OR @i_Site = '' OR ISNULL(A.Site, '') LIKE '%' + @i_Site + '%')
      AND (@i_DeliveryPlace IS NULL OR @i_DeliveryPlace = ''
           OR ISNULL(A.DeliveryPlace, '') LIKE '%' + @i_DeliveryPlace + '%')
      AND (@i_ItemNo IS NULL OR @i_ItemNo = '' OR B.ItemNo = @i_ItemNo)
    GROUP BY B.ItemNo;

    SELECT ITEMNO, ITEMNM, SPEC, NOOUTQTY, ORDQTY, OUTQTY
    FROM #Result
    ORDER BY
         CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'ITEMNO' THEN ITEMNO END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'ITEMNM' THEN ITEMNM END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'SPEC' THEN SPEC END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'ORDQTY' THEN ORDQTY END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'OUTQTY' THEN OUTQTY END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'NOOUTQTY' THEN NOOUTQTY END ASC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'ITEMNO' THEN ITEMNO END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'ITEMNM' THEN ITEMNM END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'SPEC' THEN SPEC END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'ORDQTY' THEN ORDQTY END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'OUTQTY' THEN OUTQTY END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'NOOUTQTY' THEN NOOUTQTY END DESC
        ,ITEMNM ASC, SPEC ASC, ITEMNO ASC
    OFFSET (@i_Page - 1) * @i_Limit ROWS
    FETCH NEXT @i_Limit ROWS ONLY;

    SELECT
         COUNT_BIG(*) AS TOTAL_COUNT
        ,SUM(ORDQTY) AS ORDQTY_SUM
        ,AVG(ORDQTY) AS ORDQTY_AVG
        ,MIN(ORDQTY) AS ORDQTY_MIN
        ,MAX(ORDQTY) AS ORDQTY_MAX
        ,SUM(OUTQTY) AS OUTQTY_SUM
        ,AVG(OUTQTY) AS OUTQTY_AVG
        ,MIN(OUTQTY) AS OUTQTY_MIN
        ,MAX(OUTQTY) AS OUTQTY_MAX
        ,SUM(NOOUTQTY) AS NOOUTQTY_SUM
        ,AVG(NOOUTQTY) AS NOOUTQTY_AVG
        ,MIN(NOOUTQTY) AS NOOUTQTY_MIN
        ,MAX(NOOUTQTY) AS NOOUTQTY_MAX
    FROM #Result;
END;
GO
```

<a id="mistw-proc-loge002-s-04"></a>
## `MISTW.dbo.proc_LOGE002_S_04`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **운영 snapshot 원문 우선**
원본 경로: `/Users/cigro/Desktop/anasa/anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/1342627826_proc_LOGE002_S_04.sql`
운영 snapshot은 저장소에 포함되지 않은 읽기 전용 스냅샷이며, 이 문서에는 해당 snapshot 파일을 그대로 보관한다.

```sql
-- Database: MISTW
-- Object: [dbo].[proc_LOGE002_S_04]
-- Type: SQL_STORED_PROCEDURE
SET ANSI_NULLS ON;
GO
SET QUOTED_IDENTIFIER ON;
GO
CREATE PROCEDURE [dbo].[proc_LOGE002_S_04]
     @i_OrdDteFr        varchar(8)
    ,@i_OrdDteTo        varchar(8)
    ,@i_ItemGrpCd       varchar(3)
    ,@i_CustNo          varchar(10)
    ,@i_EquipCustNm     varchar(100)
    ,@i_Site            varchar(100)
    ,@i_DeliveryPlace   varchar(100)
    ,@i_ItemNo          varchar(50)
    ,@i_Page            int = 1
    ,@i_Limit           int = 2147483647
    ,@i_SortBy          varchar(50) = ''
    ,@i_SortOrder       varchar(4) = 'ASC'
AS
BEGIN
    SET NOCOUNT ON;

    SET @i_Page = CASE WHEN ISNULL(@i_Page, 0) < 1 THEN 1 ELSE @i_Page END;
    SET @i_Limit = CASE WHEN ISNULL(@i_Limit, 0) < 1 THEN 2147483647 ELSE @i_Limit END;
    SET @i_SortBy = UPPER(ISNULL(@i_SortBy, ''));
    SET @i_SortOrder = CASE WHEN UPPER(ISNULL(@i_SortOrder, 'ASC')) = 'DESC'
                            THEN 'DESC' ELSE 'ASC' END;

    ;WITH ActualOut AS (
        SELECT y.OrdEnrDtlNo, SUM(ISNULL(y.OutQty, 0)) AS OutQty
        FROM MISTW.dbo.tbLogOutDtl AS y
        GROUP BY y.OrdEnrDtlNo
    )
    SELECT
         D.CustNm AS CUSTNM
        ,MISTW.dbo.fn_comCodeNm('LOG003', A.TaxGb, '1') AS TAXGB
        ,ISNULL(A.EquipCustNm, '') AS EQUIPCUSTNM
        ,ISNULL(A.Site, '') AS SITE
        ,ISNULL(A.DeliveryPlace, '') AS DELIVERYPLACE
        ,MISTW.dbo.fn_logOrdNm(A.CustCd, A.OrdEmpNo) AS ORDEMPNM
        ,MISCM.dbo.FN_GFNDATETYPETRANS(A.DeliveryReqYmd, '-') AS DELIVERYREQYMD
        ,CONVERT(varchar(10), '') AS OUTDUEDTE
        ,MISTW.dbo.fn_comCodeNm('LOG006', A.InputGb, '1') AS INPUTGB
        ,CONVERT(decimal(38, 6), ISNULL(B.OrdQty, 0)) AS ORDQTY
        ,CONVERT(decimal(38, 6), ISNULL(E.OutQty, 0)) AS OUTQTY
        ,CONVERT(decimal(38, 6), ISNULL(B.OrdQty, 0) - ISNULL(E.OutQty, 0)) AS NOOUTQTY
        ,A.OrdEnrNo AS _ORDENRNO
        ,B.OrdEnrDtlNo AS _ORDENRDTLNO
    INTO #Result
    FROM MISTW.dbo.tbLogOrdEnr AS A
    INNER JOIN MISTW.dbo.tbLogOrdEnrDtl AS B
        ON A.OrdEnrNo = B.OrdEnrNo
    INNER JOIN MISPD.dbo.PDItemMaster AS C
        ON B.ItemNo = C.ItemNo
    INNER JOIN MISSA.dbo.SACust AS D
        ON A.CustCd = D.CustCd
    LEFT JOIN ActualOut AS E
        ON E.OrdEnrDtlNo = B.OrdEnrDtlNo
    WHERE A.DeliveryReqYmd BETWEEN @i_OrdDteFr AND @i_OrdDteTo
      AND ISNULL(B.OutStat, '') NOT IN ('04', '99')
      AND A.OrdType NOT IN ('02', '03')
      AND ISNULL(B.OrdItemRecYn, '') = 'Y'
      AND ISNULL(B.CloseYn, '') <> 'Y'
      AND ISNULL(B.OrdQty, 0) - ISNULL(E.OutQty, 0) <> 0
      AND B.ItemNo = ISNULL(@i_ItemNo, '')
      AND (@i_ItemGrpCd IS NULL OR @i_ItemGrpCd IN ('', '%') OR C.ItemGrpCd = @i_ItemGrpCd)
      AND (@i_CustNo IS NULL OR @i_CustNo = '' OR A.CustCd = @i_CustNo)
      AND (@i_EquipCustNm IS NULL OR @i_EquipCustNm = ''
           OR ISNULL(A.EquipCustNm, '') LIKE '%' + @i_EquipCustNm + '%')
      AND (@i_Site IS NULL OR @i_Site = '' OR ISNULL(A.Site, '') LIKE '%' + @i_Site + '%')
      AND (@i_DeliveryPlace IS NULL OR @i_DeliveryPlace = ''
           OR ISNULL(A.DeliveryPlace, '') LIKE '%' + @i_DeliveryPlace + '%');

    SELECT
         CUSTNM, TAXGB, EQUIPCUSTNM, SITE, DELIVERYPLACE, ORDEMPNM
        ,DELIVERYREQYMD, OUTDUEDTE, INPUTGB, ORDQTY, OUTQTY, NOOUTQTY
    FROM #Result
    ORDER BY
         CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'CUSTNM' THEN CUSTNM END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'TAXGB' THEN TAXGB END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'EQUIPCUSTNM' THEN EQUIPCUSTNM END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'SITE' THEN SITE END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'DELIVERYPLACE' THEN DELIVERYPLACE END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'ORDEMPNM' THEN ORDEMPNM END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'DELIVERYREQYMD' THEN DELIVERYREQYMD END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'OUTDUEDTE' THEN OUTDUEDTE END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'INPUTGB' THEN INPUTGB END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'ORDQTY' THEN ORDQTY END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'OUTQTY' THEN OUTQTY END ASC
        ,CASE WHEN @i_SortOrder = 'ASC' AND @i_SortBy = 'NOOUTQTY' THEN NOOUTQTY END ASC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'CUSTNM' THEN CUSTNM END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'TAXGB' THEN TAXGB END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'EQUIPCUSTNM' THEN EQUIPCUSTNM END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'SITE' THEN SITE END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'DELIVERYPLACE' THEN DELIVERYPLACE END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'ORDEMPNM' THEN ORDEMPNM END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'DELIVERYREQYMD' THEN DELIVERYREQYMD END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'OUTDUEDTE' THEN OUTDUEDTE END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'INPUTGB' THEN INPUTGB END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'ORDQTY' THEN ORDQTY END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'OUTQTY' THEN OUTQTY END DESC
        ,CASE WHEN @i_SortOrder = 'DESC' AND @i_SortBy = 'NOOUTQTY' THEN NOOUTQTY END DESC
        ,CUSTNM ASC, TAXGB ASC, _ORDENRNO ASC, _ORDENRDTLNO ASC
    OFFSET (@i_Page - 1) * @i_Limit ROWS
    FETCH NEXT @i_Limit ROWS ONLY;

    SELECT
         COUNT_BIG(*) AS TOTAL_COUNT
        ,SUM(ORDQTY) AS ORDQTY_SUM
        ,AVG(ORDQTY) AS ORDQTY_AVG
        ,MIN(ORDQTY) AS ORDQTY_MIN
        ,MAX(ORDQTY) AS ORDQTY_MAX
        ,SUM(OUTQTY) AS OUTQTY_SUM
        ,AVG(OUTQTY) AS OUTQTY_AVG
        ,MIN(OUTQTY) AS OUTQTY_MIN
        ,MAX(OUTQTY) AS OUTQTY_MAX
        ,SUM(NOOUTQTY) AS NOOUTQTY_SUM
        ,AVG(NOOUTQTY) AS NOOUTQTY_AVG
        ,MIN(NOOUTQTY) AS NOOUTQTY_MIN
        ,MAX(NOOUTQTY) AS NOOUTQTY_MAX
    FROM #Result;
END;
GO
```

<a id="mistw-proc-logg023-s-01"></a>
## `MISTW.dbo.proc_LOGG023_S_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_LOGG023_S_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_LOGG023_S_01.sql

```sql

/********************************************************************************
제    목 : 판매월보조회
프로그램 : 판매월보(전체)
등 록 일 : 2019-03-15
등 록 자 : 김은기
수정일		수정자		내용
20210712	이해권		플랙시블 품목 추가
20231130	안진주		공구 판매 품목명 (대분류명 -> 분류명) 변경 작업 / (ClassNm을 품명으로 사용하고 있던 기존 업무를 기준으로 공구 판매에도 타 주문 유형과 동일하게 ClassNm을 품명으로 조회하는 함수 적용)
-----------------------------------------------------------------------
EXEC  MISTW.dbo.proc_LOGG023_S_01 '%', 'AA030', '20190501', '20190531', '0'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_LOGG023_S_01] @iOrdGb          VARCHAR(10)    --주문유형
        , @iCustCd         VARCHAR(10)    --거래처코드
        , @iOutYmdFr       VARCHAR(8)     --출고일자fr
		, @iOutYmdTo       VARCHAR(8)     --출고일자to
		, @iKind           VARCHAR(1)     --소계여부(체크되면 1,안되면 0)
AS

DECLARE @w_OutYmdFr     VARCHAR(8)

DECLARE @tmpOut TABLE (
		OutYmd			VARCHAR(10),		-- 출고일자
		OrdGB			VARCHAR(50),		-- 주문유형
		OutNo			VARCHAR(50),		-- 출고번호
		CustNm			VARCHAR(50),		-- 거래처명
		EquipCustNm		VARCHAR(200),		-- 설비업체
		Site			VARCHAR(200),		-- 현장
		DeliveryPlace	VARCHAR(200),		-- 화물지점
		TelNo			VARCHAR(50),		-- 전화번호
		ItemNm			VARCHAR(50),		-- 품목명
		Spec			VARCHAR(50),		-- 규격
		OutQty			INT,				-- 출고수량
		SalesPrice		MONEY,				-- 단가
		SupplyAmt		MONEY,				-- 공급가액
		Vat				MONEY,				-- 부가세
		Amt				MONEY,				-- 합계
		ItemNo			VARCHAR(50),		-- 품목코드(H)
		CustCd			VARCHAR(10),		-- 거래처코드(H)
		ORD				INT,				-- 정렬순번(H)
		ORD2			INT					-- 정렬순번2
)

BEGIN

	--SET @w_OutYmdFr	= LEFT(@iOutYmdTo,6) +'01'	
	SET @w_OutYmdFr	= @iOutYmdFr

	SET NOCOUNT ON;


	--물류출고건
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(a.OutYmd, '-')
		 , a.ItemGrpCd + '_제품'
		 , a.OutNo
		 , d.CustNm
		 , a.EquipCustNm
		 , a.Adr
		 , a.DeliveryPlace
		 , a.TelNo
		 , e.ItemNm
		 , e.Spec
		 , b.OutQty
		 , b.SalesPrice
		 , b.SupplyAmt
		 , b.Vat
		 , b.Amt
		 , b.ItemNo
		 , a.CustCd
		 , 0
		 , 0
	FROM   MISTW.dbo.tbLogOut a
	INNER JOIN MISTW.dbo.tbLogOutDtl b ON b.OutEnrNo = a.OutEnrNo
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = a.CustCd
	INNER JOIN MISPD.dbo.PDItemMaster e ON e.ItemNo = b.ItemNo
	WHERE  a.OutYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    a.CustCd = @iCustCd
	AND    b.ItemNo NOT IN ('XS1-HM01','5XS1-HM02','8FXS1-HM03')

	--물류화물비(일별합산)
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(a.OutYmd, '-')
		 , a.ItemGrpCd + '_물류화물비'
		 , ''
		 , d.CustNm
		 , ''
		 , ''
		 , ''
		 , ''
		 , '물류화물비'
		 , ''
		 , 0
		 , 0
		 , SUM(ISNULL(b.SupplyAmt, 0))
		 , SUM(ISNULL(b.Vat, 0))
		 , SUM(ISNULL(b.Amt, 0))
		 , ''
		 , a.CustCd
		 , 0
		 , 2
	FROM   MISTW..tbLogOut a
	INNER JOIN MISTW.dbo.tbLogOutDtl b ON b.OutEnrNo = a.OutEnrNo
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = a.CustCd
	WHERE  a.OutYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    a.CustCd = @iCustCd
	AND    b.ItemNo IN ('XS1-HM01','5XS1-HM02','8FXS1-HM03')
	GROUP BY a.OutYmd, a.CustCd, a.ItemGrpCd, d.CustNm
	HAVING SUM(ISNULL(b.Amt, 0)) <> 0

	-- 공구 판매
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(b.IssueYmd, '-')
		 , a.ItemGrpCd + '_공구판매'
		 , b.DeliveryNo
		 , MISTW.dbo.fn_comCustNm(a.CustCd)
		 , MISTW.dbo.fn_gtcEquipNm(a.EquipCustCd)
		 , MISTW.dbo.fn_gtcSiteNm(a.SiteCd)
		 , MISTW.dbo.fn_gtcDPNm(a.DeliveryPlace)
		 , a.TelNo
		 --, c.ClassLNm
		 -- 20231130 안진주 공구 판매 품목명 (대분류명 -> 분류명) 변경 작업 / (ClassNm을 품명으로 사용하고 있던 기존 업무를 기준으로 공구 판매에도 타 주문 유형과 동일하게 ClassNm을 품명으로 조회하는 함수 적용)
		 , MISTW.dbo.fn_gtcToolNm('2', a.ItemGrpCd, b.LotNo)
		 , c.Spec
		 , b.Qty
		 , b.Price
		 , b.SupAmt
		 , b.Vat
		 , b.Amt
		 , b.LotNo
		 , a.CustCd
		 , 0
		 , 4
	FROM   MISTO.dbo.TODeliveryMaster a
	INNER JOIN MISTO.dbo.TODeliveryDetail b ON b.DeliveryNo = a.DeliveryNo
	INNER JOIN MISTO.dbo.TOClass c ON c.ClassCd = b.ClassCd AND c.ItemGrpCd = b.ItemGrpCd
	INNER JOIN MISTW.dbo.tbGtcOrdEnr o ON o.DeliveryNo = a.DeliveryNo
	WHERE  a.DeliveryYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    a.CustCd = @iCustCd
	AND    o.OrdGB IN ('SP02', 'GP02', 'FP02')

	-- SP공구임대료
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(b.ThisMonDeadYmd, '-')
		 , a.ItemGrpCd + '_임대료'
		 , ''
		 , d.CustNm
		 , a.EQUIPCUSTNM
		 , a.SITENM
		 , ''
		 , ''
		 , ''
		 , ''
		 , ''
		 , ''
		 , SUM(CONVERT(money, a.RAMT))
		 , SUM(ROUND(CONVERT(money, a.RAMT) / 10, 0))
		 , SUM(CONVERT(money, a.RAMT) + ROUND(CONVERT(money, a.RAMT) / 10, 0))
		 , ''
		 , a.CUSTCD
		 , 0
		 , 6
	FROM   MISTO.dbo.TORentCalcHistory a
	INNER JOIN MISTO.dbo.TOCustDeadYmdMgm b ON b.CustCd = a.CUSTCD AND b.CalYm = a.STANDARDYM
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = a.CUSTCD
	WHERE  a.STANDARDYM BETWEEN LEFT(@iOutYmdFr, 6) AND LEFT(@iOutYmdTo, 6)
	AND    b.ThisMonDeadYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    a.CUSTCD = @iCustCd
	AND    b.ItemGrpCd = 'SP'
	GROUP BY a.EQUIPCUSTNM, a.SITENM, b.ThisMonDeadYmd, a.CUSTCD, a.STANDARDYM, a.ItemGrpCd, d.CustNm
	--20200810 / -건이 발생하여 금액이 0이 아닌 건을 집계하도록 변경
	--HAVING SUM(CONVERT(money, a.RAMT)) > 0
	HAVING SUM(CONVERT(money, a.RAMT)) <> 0
	
	-- GP공구임대료
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(b.ThisMonDeadYmd, '-')
		 , a.ItemGrpCd + '_임대료'
		 , ''
		 , d.CustNm
		 , a.EQUIPCUSTNM
		 , a.SITENM
		 , ''
		 , ''
		 , ''
		 , ''
		 , ''
		 , ''
		 , SUM(CONVERT(money, a.RAMT))
		 , SUM(ROUND(CONVERT(money, a.RAMT) / 10, 0))
		 , SUM(CONVERT(money, a.RAMT) + ROUND(CONVERT(money, a.RAMT) / 10, 0))
		 , ''
		 , a.CUSTCD
		 , 0
		 , 8
	FROM   MISTO.dbo.TORentCalcHistoryGP a
	INNER JOIN MISTO.dbo.TOCustDeadYmdMgm b ON b.CustCd = a.CUSTCD AND b.CalYm = a.STANDARDYM
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = a.CUSTCD
	WHERE  a.STANDARDYM BETWEEN LEFT(@iOutYmdFr, 6) AND LEFT(@iOutYmdTo, 6)
	AND    b.ThisMonDeadYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    a.CUSTCD = @iCustCd
	AND    b.ItemGrpCd = 'GP'
	GROUP BY a.EQUIPCUSTNM, a.SITENM, b.ThisMonDeadYmd, a.CUSTCD, a.STANDARDYM, a.ItemGrpCd, d.CustNm
	HAVING SUM(CONVERT(money, a.RAMT)) > 0

	-- FP공구임대료
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(b.ThisMonDeadYmd, '-')
		 , a.ItemGrpCd + '_임대료'
		 , ''
		 , d.CustNm
		 , a.EQUIPCUSTNM
		 , a.SITENM
		 , ''
		 , ''
		 , ''
		 , ''
		 , ''
		 , ''
		 , SUM(CONVERT(money, a.RAMT))
		 , SUM(ROUND(CONVERT(money, a.RAMT) / 10, 0))
		 , SUM(CONVERT(money, a.RAMT) + ROUND(CONVERT(money, a.RAMT) / 10, 0))
		 , ''
		 , a.CUSTCD
		 , 0
		 , 8
	FROM   MISTO.dbo.TORentCalcHistoryFP a
	INNER JOIN MISTO.dbo.TOCustDeadYmdMgm b ON b.CustCd = a.CUSTCD AND b.CalYm = a.STANDARDYM
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = a.CUSTCD
	WHERE  a.STANDARDYM BETWEEN LEFT(@iOutYmdFr, 6) AND LEFT(@iOutYmdTo, 6)
	AND    b.ThisMonDeadYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    a.CUSTCD = @iCustCd
	AND    b.ItemGrpCd = 'FP'
	GROUP BY a.EQUIPCUSTNM, a.SITENM, b.ThisMonDeadYmd, a.CUSTCD, a.STANDARDYM, a.ItemGrpCd, d.CustNm
	HAVING SUM(CONVERT(money, a.RAMT)) > 0

	-- AS연체료
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(a.CalcYmd, '-')
		 , a.ItemGrpCd + '_AS연체료'
		 , ''
		 , d.CustNm
		 , MISTW.dbo.fn_gtcEquipNm(a.EquipCustCd)
		 , MISTW.dbo.fn_gtcSiteNm(a.SiteCd)
		 , MISTW.dbo.fn_gtcDPNm(a.DeliveryPlace)
		 , ''
		 , MISTW.dbo.fn_gtcToolNm('2', a.ItemGrpCd, b.LotNo)
		 , MISTW.dbo.fn_gtcToolSpec('2', a.ItemGrpCd, b.LotNo)
		 , 0
		 , 0
		 , b.RealLateFee
		 , ROUND(b.RealLateFee * 0.1, 0)
		 , b.RealLateFee + ROUND(b.RealLateFee * 0.1, 0)
		 , b.LotNo
		 , a.CustCd
		 , 1
		 , 0
	FROM   MISTO.dbo.tbASLate a
	INNER JOIN MISTO.dbo.tbASLateDtl b ON b.AsLateMgmNo = a.AsLateMgmNo
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = a.CustCd
	WHERE  a.CalcYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    a.CustCd = @iCustCd

	-- AS청구비
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(a.SubmitYmd, '-')
		 , b.ItemGrpCd + '_AS청구비'
		 , ''
		 , d.CustNm
		 , MISTW.dbo.fn_gtcEquipNm(c.EquipCustCd)
		 , MISTW.dbo.fn_gtcSiteNm(c.SiteCd)
		 , MISTW.dbo.fn_gtcDPNm(c.DeliveryPlace)
		 , ''
		 , MISTW.dbo.fn_gtcToolNm('2', b.ItemGrpCd, b.LotNo)
		 , MISTW.dbo.fn_gtcToolSpec('2', b.ItemGrpCd, b.LotNo)
		 , 1
		 , SUM(ISNULL(e.Price, 0))
		 , SUM(e.SupAmt)
		 , ROUND(SUM(e.SupAmt * 0.1), 0)
		 , SUM(e.SupAmt) + ROUND(SUM(e.SupAmt * 0.1), 0)
		 , ''
		 , b.CustCD
		 , 1
		 , 2
	FROM   MISTO.dbo.TOAsFault a
	INNER JOIN MISTO.dbo.TOAs b ON b.AsNo = a.AsNo
	INNER JOIN MISTO.dbo.TODeliveryMaster c ON c.DeliveryNo = b.DeliveryNo
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = c.CustCd
	INNER JOIN MISTO.dbo.TOAsPart e ON e.AsNo = a.AsNo AND e.Seq = a.Seq
	WHERE  a.SubmitYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    b.CustCD = @iCustCd
	AND    a.SubmitYn = 'Y'		-- 청구여부
	AND    e.SupAmt > 0
	GROUP BY a.SubmitYmd, b.ItemGrpCd, b.CustCD, c.EquipCustCd, c.SiteCd, c.DeliveryPlace, b.LotNo, d.CustNm

	-- 공구분실
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(a.LossYmd, '-')
		 , a.ItemGrpCd + '_공구분실'
		 , ''
		 , d.CustNm
		 , MISTW.dbo.fn_gtcEquipNm(c.EquipCustCd)
		 , MISTW.dbo.fn_gtcSiteNm(c.SiteCd)
		 , MISTW.dbo.fn_gtcDPNm(c.DeliveryPlace)
		 , ''
		 , MISTW.dbo.fn_gtcToolNm('2', a.ItemGrpCd, a.LotNo)
		 , MISTW.dbo.fn_gtcToolSpec('2', a.ItemGrpCd, a.LotNo)
		 , 1
		 , 0
		 , a.Amt
		 , ROUND(a.Amt * 0.1, 0)
		 , a.Amt + ROUND(a.Amt * 0.1, 0)
		 , a.LotNo
		 , c.CustCd
		 , 1
		 , 4
	FROM   MISTO.dbo.ToLoss a
	INNER JOIN MISTO.dbo.TODeliveryMaster c ON c.DeliveryNo = a.DeliveryNo
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = c.CustCd
	WHERE  a.LossYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    c.CustCd = @iCustCd
	AND    a.Amt > 0
 
	-- 공구분실취소
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(a.CancelYmd, '-')
		 , a.ItemGrpCd + '_공구분실취소'
		 , ''
		 , d.CustNm
		 , MISTW.dbo.fn_gtcEquipNm(c.EquipCustCd)
		 , MISTW.dbo.fn_gtcSiteNm(c.SiteCd)
		 , MISTW.dbo.fn_gtcDPNm(c.DeliveryPlace)
		 , ''
		 , MISTW.dbo.fn_gtcToolNm('2', a.ItemGrpCd, a.LotNo)
		 , MISTW.dbo.fn_gtcToolSpec('2', a.ItemGrpCd, a.LotNo)
		 , 1
		 , 0
		 , -a.Amt
		 , -(ROUND(a.Amt * 0.1, 0))
		 , -(a.Amt + (ROUND(a.Amt * 0.1, 0)))
		 , a.LotNo
		 , c.CustCd
		 , 1
		 , 6
	FROM   MISTO.dbo.ToLoss a
	INNER JOIN MISTO.dbo.TODeliveryMaster c ON c.DeliveryNo = a.DeliveryNo
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = c.CustCd
	WHERE  a.CancelYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    c.CustCd = @iCustCd
	AND    a.Amt > 0

	-- 기타청구
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(a.DeliveryYmd, '-')
		 , a.ItemGrpCd + '_기타청구'
		 , ''
		 , d.CustNm
		 , MISTW.dbo.fn_gtcEquipNm(a.EquipCustCd)
		 , MISTW.dbo.fn_gtcSiteNm(a.SiteCd)
		 , MISTW.dbo.fn_gtcDPNm(a.DeliveryPlace)
		 , ''
		 , b.ItemDNm
		 , ''
		 , 0
		 , 0
		 , b.SupplyAmt
		 , b.Vat
		 , b.Amt
		 , ''
		 , a.CustCd
		 , 1
		 , 8
	FROM   MISTO.dbo.tbGtcTrade a
	INNER JOIN MISTO.dbo.tbGtcTradeDetail b ON b.TradeMgmNo = a.TradeMgmNo
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = a.CustCd
	WHERE  a.DeliveryYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    a.CustCd = @iCustCd

	-- 공구화물비
	INSERT INTO @tmpOut
	SELECT MISCM.dbo.fn_gfnDateTypeTrans(e.DeliveryYmd, '-')
		 , a.ItemGrpCd + '_공구화물비'
		 , ''
		 , d.CustNm
		 , ''
		 , ''
		 , ''
		 , ''
		 , '공구화물비'
		 , ''
		 , 0
		 , 0
		 , SUM(ISNULL(b.TransitFee, 0))
		 , SUM(ISNULL(b.TransitVat, 0))
		 , SUM(ISNULL(b.TransitSum, 0))
		 , ''
		 , a.CustCd
		 , 2
		 , 0
	FROM   MISTW.dbo.tbGtcOrdEnr a
	INNER JOIN MISTW.dbo.tbGtcPkgEnr b ON b.OrdEnrNo = a.OrdEnrNo
	INNER JOIN MISSA.dbo.SACust d ON d.CustCd = a.CustCd
	CROSS APPLY MISTW.dbo.fn_ordDeliveryYmd(a.OrdEnrNo, RIGHT(a.OrdGB, 2)) e
	WHERE  e.DeliveryYmd BETWEEN @iOutYmdFr AND @iOutYmdTo
	AND    a.CustCd = @iCustCd
	AND    a.OutStat = '04' 
	GROUP BY e.DeliveryYmd, a.CustCd, a.ItemGrpCd, d.CustNm
	HAVING SUM(ISNULL(b.TransitSum, 0)) <> 0


	IF @iKind = '0'
	BEGIN
		SELECT *
		FROM   @tmpOut
		WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		OR    (@iOrdGb = '3' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		OR    (@iOrdGb = '4' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		OR    (@iOrdGb = '%')
		--WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		--OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		--OR    (@iOrdGb = '3' AND ORDGB = 'FP01')
		--OR    (@iOrdGb = '4' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		--OR    (@iOrdGb = '5' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		--OR    (@iOrdGb = '6' AND ORDGB IN ('FP02', 'FP03', 'FP04', 'FP05', 'FP06'))
		--OR    (@iOrdGb = '%')
		UNION ALL
		SELECT OUTYMD
			 , '출고번호 소계'
			 , OUTNO + ' 출고번호 소계'
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , 0
			 , 0
			 , SUM(SUPPLYAMT)
			 , SUM(VAT)
			 , SUM(AMT)
			 , ''
			 , ''
			 , ORD
			 , CASE WHEN ORD2 = 0 THEN ORD2 ELSE ORD2 + 1 END
		FROM   @tmpOut
		WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		OR    (@iOrdGb = '3' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		OR    (@iOrdGb = '4' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		OR    (@iOrdGb = '%')
		--WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		--OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		--OR    (@iOrdGb = '3' AND ORDGB = 'FP01')
		--OR    (@iOrdGb = '4' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		--OR    (@iOrdGb = '5' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		--OR    (@iOrdGb = '6' AND ORDGB IN ('FP02', 'FP03', 'FP04', 'FP05', 'FP06'))
		--OR    (@iOrdGb = '%')
		GROUP BY OUTYMD, OUTNO, ORD, ORD2
		UNION ALL
		SELECT OUTYMD
			 , '일별 소계'
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , 0
			 , 0
			 , SUM(SUPPLYAMT)
			 , SUM(VAT)
			 , SUM(AMT)
			 , ''
			 , ''
			 , 3
			 , 0
		FROM   @tmpOut
		WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		OR    (@iOrdGb = '3' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		OR    (@iOrdGb = '4' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		OR    (@iOrdGb = '%')
		--WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		--OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		--OR    (@iOrdGb = '3' AND ORDGB = 'FP01')
		--OR    (@iOrdGb = '4' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		--OR    (@iOrdGb = '5' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		--OR    (@iOrdGb = '6' AND ORDGB IN ('FP02', 'FP03', 'FP04', 'FP05', 'FP06'))
		--OR    (@iOrdGb = '%')
		GROUP BY OUTYMD
		UNION ALL
		SELECT MISCM.dbo.fn_gfnDateTypeTrans(@iOutYmdTo, '-')
			 , '총계'
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , 0
			 , 0
			 , SUM(SUPPLYAMT)
			 , SUM(VAT)
			 , SUM(AMT)
			 , ''
			 , ''
			 , 4
			 , 0
		FROM   @tmpOut
		WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		OR    (@iOrdGb = '3' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		OR    (@iOrdGb = '4' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		OR    (@iOrdGb = '%')
		--WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		--OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		--OR    (@iOrdGb = '3' AND ORDGB = 'FP01')
		--OR    (@iOrdGb = '4' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		--OR    (@iOrdGb = '5' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		--OR    (@iOrdGb = '6' AND ORDGB IN ('FP02', 'FP03', 'FP04', 'FP05', 'FP06'))
		--OR    (@iOrdGb = '%')
		ORDER BY OUTYMD, ORD, ORD2, OUTNO 
	END
	ELSE
	BEGIN
		SELECT *
		FROM   @tmpOut
		WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		OR    (@iOrdGb = '3' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		OR    (@iOrdGb = '4' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		OR    (@iOrdGb = '%')
		--WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		--OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		--OR    (@iOrdGb = '3' AND ORDGB = 'FP01')
		--OR    (@iOrdGb = '4' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		--OR    (@iOrdGb = '5' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		--OR    (@iOrdGb = '6' AND ORDGB IN ('FP02', 'FP03', 'FP04', 'FP05', 'FP06'))
		--OR    (@iOrdGb = '%')
		UNION ALL
		SELECT MISCM.dbo.fn_gfnDateTypeTrans(@iOutYmdTo, '-')
			 , '총계'
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , ''
			 , 0
			 , 0
			 , SUM(SUPPLYAMT)
			 , SUM(VAT)
			 , SUM(AMT)
			 , ''
			 , ''
			 , 4
			 , 0
		FROM   @tmpOut
		WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		OR    (@iOrdGb = '3' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		OR    (@iOrdGb = '4' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		OR    (@iOrdGb = '%')
		--WHERE (@iOrdGb = '1' AND ORDGB = 'SP01')
		--OR    (@iOrdGb = '2' AND ORDGB = 'GP01')
		--OR    (@iOrdGb = '3' AND ORDGB = 'FP01')
		--OR    (@iOrdGb = '4' AND ORDGB IN ('SP02', 'SP03', 'SP04', 'SP05', 'SP06'))
		--OR    (@iOrdGb = '5' AND ORDGB IN ('GP02', 'GP03', 'GP04', 'GP05', 'GP06'))
		--OR    (@iOrdGb = '6' AND ORDGB IN ('FP02', 'FP03', 'FP04', 'FP05', 'FP06'))
		--OR    (@iOrdGb = '%')
		ORDER BY OUTYMD, ORD, ORD2, OUTNO 
	END










/*
BEGIN

	--SET @w_OutYmdFr	= LEFT(@iOutYmdTo,6) +'01'	

	SET @w_OutYmdFr	= @iOutYmdFr

	SET NOCOUNT ON;
	IF @iKind = '0'
		BEGIN	  
			   --물류출고건
			SELECT X.OutYmd  --출고일자
		   		 , OrdGB --주문유형
		   		 , X.OutNo   --출고번호
		   		 , X.CustNm  --거래처명	 
		   		 , X.EquipCustNm --설비업체
		   		 , X.Site        --현장
		   		 , X.DeliveryPlace --화물지점
		   		 , X.TelNo         --전화번호
		   		 , X.ItemNm        --품목명
		   		 , X.Spec          --규격
		   		 , X.OutQty        --출고수량
		   		 , X.SalesPrice    --단가
		   		 , X.SupplyAmt     --공급가액
		   		 , X.Vat           --부가세
		   		 , X.Amt           --합계
		   		 , X.ItemNo        --품목코드(H)
		   		 , X.CustCd        --거래처코드(H)
		   		 , ORD             --정렬순번(H)
				 , ORD2            --정렬순번2
		   		 , CASE WHEN OrdGB IN('출고번호 소계','소계','총계') THEN 0 ELSE X.SupplyAmt END AS SUPPLYAMTSUM	--소계 포함 되지 않은 공급가액 합계
		   		 , CASE WHEN OrdGB IN('출고번호 소계','소계','총계') THEN 0 ELSE X.Vat	   END AS VATSUM		--소계 포함 되지 않은 부가세 합계
		   		 , CASE WHEN OrdGB IN('출고번호 소계','소계','총계') THEN 0 ELSE X.Amt       END AS AMTSUM		--소계 포함 되지 않은 합계의 합계
			   FROM(

				SELECT X.OutYmd  --출고일자
					 , X.ORDGB --ISNULL(MISTW.DBO.fn_comCodeNm('LOG005',X.OrdGB,1),'')  AS OrdGB --주문유형
					 , X.OutNo   --출고번호
					 , X.CustNm  --거래처명	 
					 , X.EquipCustNm --설비업체
					 , X.Site        --현장
					 , X.DeliveryPlace --화물지점
					 , X.TelNo         --전화번호
					 , X.ItemNm        --품목명
					 , X.Spec          --규격
					 , X.OutQty        --출고수량
					 , X.SalesPrice    --단가
					 , X.SupplyAmt     --공급가액
					 , X.Vat           --부가세
					 , X.Amt           --합계
					 , X.ItemNo        --품목코드(H)
					 , X.CustCd        --거래처코드(H)
					 , ORD             --정렬순번(H)
					 , ORD2
				 FROM
				(
				SELECT 
					  A.OutYmd  --출고일자
					 , A.ITEMGRPCD + '_제품'   AS ORDGB
					 , A.OutNo   --출고번호
					 , D.CustNm  --거래처명	 
					 , A.EquipCustNm --설비업체
					 , A.Adr AS SITE        --현장
					 , A.DeliveryPlace --화물지점
					 , A.TelNo         --전화번호
					 , E.ItemNm        --품목명
					 , E.Spec          --규격
					 , B.OutQty        --출고수량
					 , B.SalesPrice    --단가
					 , B.SupplyAmt     --공급가액
					 , B.Vat           --부가세
					 , B.Amt           --합계
					 , B.ItemNo        --품목코드
					 , A.CustCd        --거래처코드
					 , 0 AS ORD             --정렬순번(H)
					 , 0 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbLogOut A
				  INNER JOIN MISTW..tbLogOutDtl B
				  ON A.OutEnrNo = B.OutEnrNo 
				  INNER JOIN MISSA..SACust D
				  ON A.CustCd  = D.CustCd 
				  INNER JOIN MISPD..PDItemMaster E
				  ON B.ITEMNO = E.ITEMNO			  
			   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
				 AND B.ItemNo NOT IN ('XS1-HM01','5XS1-HM02')
				 AND A.CustCd = @iCustCd
				----물류(일자/거래처/설비업체별 소계)
				--UNION ALL
				--SELECT 
				--	  A.OutYmd  --출고일자
				--	 , A.ITEMGRPCD + '_제품'   AS ORDGB
				--	 , A.OutNo   --출고번호
				--	 , D.CustNm  --거래처명	 
				--	 , A.EquipCustNm --설비업체
				--	 , '' AS SITE        --현장
				--	 , '' AS DeliveryPlace --화물지점
				--	 , '' AS TelNo         --전화번호
				--	 , '' AS ItemNm        --품목명
				--	 , '' AS Spec          --규격
				--	 , SUM(B.OutQty) AS OutQty         --출고수량
				--	 , 0  AS SalesPrice    --단가
				--	 , SUM(B.SupplyAmt) AS SupplyAmt     --공급가액
				--	 , SUM(B.Vat) AS VAT           --부가세
				--	 , SUM(B.Amt) AS AMT           --합계
				--	 , ''         AS ItemNo        --품목코드
				--	 , A.CustCd        --거래처코드
				--	 , 0 AS ORD             --정렬순번(H)
				--	 , 1 AS ORD             --정렬순번(H)
				--  FROM MISTW..tbLogOut A
				--  INNER JOIN MISTW..tbLogOutDtl B
				--  ON A.OutEnrNo = B.OutEnrNo 
				--  INNER JOIN MISSA..SACust D
				--  ON A.CustCd  = D.CustCd 
				--  INNER JOIN MISPD..PDItemMaster E
				--  ON B.ITEMNO = E.ITEMNO			  
			 --  WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
				-- AND B.ItemNo NOT IN ('XS1-HM01','5XS1-HM02')
				-- AND A.CustCd = @iCustCd
				-- GROUP BY A.OutYmd,A.OutNo,A.ITEMGRPCD + '_제품',D.CustNm,A.EquipCustNm,A.CustCd
				--물류화물비(일별합산)
				UNION ALL
				SELECT 
					  A.OutYmd    --출고일자
					 , A.ITEMGRPCD + '_물류화물비'   --주문유형
					 , ''           --출고번호
					 , MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명 
					 , ''           --설비업체
					 , ''           --현장
					 , ''           --화물지점
					 , ''           --전화번호
					 , '물류화물비'        --품목명
					 , ''              --규격
					 , 0               --출고수량
					 , 0               --단가
					 , SUM(ISNULL(B.SupplyAmt,0)) AS SUPPLYAMT     --공급가액
					 , SUM(ISNULL(B.Vat,0))       AS VAT    --부가세
					 , SUM(ISNULL(B.Amt,0))       AS AMT    --합계
					 , ''              --품목코드
					 , A.CustCd        --거래처코드
					 , 0 AS ORD        --정렬순번(H)
					 , 2 AS ORD2        --정렬순번(H)
				  FROM MISTW..tbLogOut A
				  INNER JOIN MISTW..tbLogOutDtl B
				  ON A.OutEnrNo = B.OutEnrNo 
				  INNER JOIN MISSA..SACust D
				  ON A.CustCd  = D.CustCd 
				  INNER JOIN MISPD..PDItemMaster E
				  ON B.ITEMNO = E.ITEMNO			  
			   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
				 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				 --AND E.AcctGb ='22'      -- 계정이 운반비 인것만
				 AND B.ItemNo IN ('XS1-HM01','5XS1-HM02')
				 AND A.CustCd = @iCustCd
			   GROUP BY A.OutYmd ,A.CustCd,A.ITEMGRPCD
			   HAVING SUM(ISNULL(B.Amt,0)) <> 0
				UNION ALL
				--공구 판매
				SELECT	DD.IssueYmd
					 , A.ITEMGRPCD + '_공구판매'
					 ,	DD.DeliveryNo
					 ,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					 ,	MISTW.DBO.fn_gtcEquipNm(Y.EquipCustCd ) AS EQUIPCUSTNM
					 ,	MISTW.DBO.fn_gtcSiteNm(Y.SiteCd)         AS SITE
					 ,	MISTW.DBO.fn_gtcDpNm(Y.DeliveryPlace)  AS DeliveryPlace
					 ,	Y.TelNo
					 ,	C.ClassNM
					 ,	C.Spec
					 ,	DD.qty
					 ,	DD.Price
					 ,	DD.SupAmt
					 ,	DD.Vat
					 ,	DD.Amt
					 ,	DD.LotNo
					 ,	A.CustCd
					 , 0 AS ORD             --정렬순번(H)
					 , 4 AS ORD2             --정렬순번(H)
				  FROM MISTW..tbGtcOrdEnr A
				  JOIN MISTW..tbGtcOrdEnrDtl B ON B.OrdEnrNo = A.OrdEnrNo
				  JOIN MISTW..tbGtcSaleOut X
						ON B.OrdEnrDtlNo = X.OrdEnrDtlNo 
				  JOIN MISTO..ToDeliveryDetail DD 
						ON	DD.DeliveryNo = X.DeliveryNo 
						AND DD.Seq        = X.Seq					
				  JOIN MISTO..TODeliveryMaster Y
						ON	DD.DeliveryNo = Y.DeliveryNo 					
				  JOIN MISTO..TOClass C ON C.ClassCd = DD.ClassCD AND C.itemGrpcd = DD.ItemGrpCd
				 WHERE A.CustCd = @iCustCd
				   AND A.OrdGb IN ('SP02', 'GP02')
				   AND Y.DeliveryYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
				UNION ALL
				/*SP공구임대료*/
				SELECT  C.ThisMonDeadYmd
					, A.ITEMGRPCD +  '_임대료'
					, ''
					, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					, A.EQUIPCUSTNM 
					, A.SITENM 
					, ''
					, ''
					, ''
					, ''
					, ''
					, ''			
					, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
					, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
					, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
					, ''
					, A.CUSTCD	
					, 0 AS ORD             --정렬순번(H)
					, 6 AS ORD2             --정렬순번(H)
				FROM MISTO..TORentCalcHistory A
				INNER JOIN MISSA..SACust B
					ON A.CUSTCD = B.CustCd 
				INNER JOIN MISTO..TOCustDeadYmdMgm C
					ON A.CUSTCD = C.CustCd 
					AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					AND C.CalYm     =A.STANDARDYM
					AND C.ItemGrpCd ='SP'					
				WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)											
				  AND A.CUSTCD = @iCustCd										
				GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
				HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0
				UNION ALL
				/*GP공구임대료*/
				SELECT  C.ThisMonDeadYmd
					, A.ITEMGRPCD +  '_임대료'
					, ''
					, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					, A.EQUIPCUSTNM 
					, A.SITENM 
					, ''
					, ''
					, ''
					, ''
					, ''
					, ''			
					, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
					, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
					, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
					, ''
					, A.CUSTCD	
					, 0 AS ORD             --정렬순번(H)
					, 8 AS ORD2             --정렬순번(H)
				FROM MISTO..TORentCalcHistoryGP A
				INNER JOIN MISSA..SACust B
					ON A.CUSTCD = B.CustCd 
				INNER JOIN MISTO..TOCustDeadYmdMgm C
					ON A.CUSTCD = C.CustCd 
					AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					AND C.CalYm     = A.STANDARDYM
					AND C.ItemGrpCd ='GP'					
				WHERE A.STANDARDYM  BETWEEN LEFT(@w_OutYmdFr,6) AND LEFT(@iOutYmdTo,6)											
				  AND A.CUSTCD = @iCustCd		
				GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
				HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0   
				/*AS연체료*/
				UNION ALL
				SELECT A.CALCYMD
					, A.ITEMGRPCD +  '_AS연체료'
					,	''  AS DELIVERYNO  --출고번호
					,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					,	MISTW.DBO.fn_gtcEquipNm(A.EquipCustCd) AS EQUIPCUSTNM
					,	MISTW.DBO.fn_gtcSiteNm(A.SiteCD)         AS SITE
					,	MISTW.DBO.fn_gtcDpNm(A.DeliveryPlace)  AS DeliveryPlace
					,	''  AS TELNO 
					,	MISTW.DBO.fn_gtcToolNm('2',A.ITEMGRPCD,B.LotNo)     AS ItemNm
					,	MISTW.DBO.fn_gtcToolSpec('2',A.ITEMGRPCD,B.LotNo)   AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice 
					,   B.RealLateFee                            AS SUPPLYAMT --공급가액
					,   B.RealLateFee * 0.1                      AS VAT       --부가세
					,   B.RealLateFee + (B.RealLateFee * 0.1) AS AMT       --금액
					,	B.LotNo             AS ItemNo
					, A.CustCd       AS CUSTCD
					, 1 AS ORD             --정렬순번(H)
					, 0 AS ORD2             --정렬순번(H)				  
				FROM MISTO..tbASLate A
				INNER JOIN MISTO..tbASLateDtl B
				   ON A.AsLateMgmNo = B.AsLateMgmNo 
				INNER JOIN MISSA..SACust C
				   ON A.CUSTCD    = C.CustCd 
				WHERE 1=1 --A.CalcYm    = LEFT(@iOutYmdTo,6)					
				  AND A.CALCYMD   BETWEEN @w_OutYmdFr AND @iOutYmdTo
				  AND A.CustCd = @iCustCd 			  			
			   /*AS청구비*/
				UNION ALL
				SELECT A.SubmitYmd
					,  C.ITEMGRPCD +  '_AS청구비'
					,	''  AS DELIVERYNO  --출고번호
					,	MISTW.DBO.fn_comCustNm(C.CustCd)	AS CUSTNM
					,	MISTW.DBO.fn_gtcEquipNm(B.EquipCustCD) AS EQUIPCUSTNM
					,	MISTW.DBO.fn_gtcSiteNm(B.SiteCD)         AS SITE
					,	MISTW.DBO.fn_gtcDpNm(B.DeliveryPlace)  AS DeliveryPlace
					,	''  AS TELNO 
					,	MISTW.DBO.fn_gtcToolNm('2',C.ITEMGRPCD,C.LotNo)     AS ItemNm
					,	MISTW.DBO.fn_gtcToolSpec('2',C.ITEMGRPCD,C.LotNo)   AS Spec
					,	1                         AS OutQty
					,	SUM(ISNULL(Price,0))                          AS SalesPrice
					,   SUM(E.SupAmt)             AS SUPPLYAMT --공급가액
					,   SUM(E.SupAmt) * 0.1       AS VAT       --부가세
					,   SUM(E.SupAmt) + SUM(E.SupAmt) * 0.1            AS AMT       --금액
					,	''             AS ItemNo
					, C.CustCd         AS CUSTCD
					, 1 AS ORD             --정렬순번(H)
					, 2 AS ORD2             --정렬순번(H)			  
					FROM MISTO..TOAsFault  A
					INNER JOIN MISTO..TOAs C
					   ON A.AsNo = C.AsNo 
					INNER JOIN MISTO..TODeliveryMaster B
					   ON C.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust D
					   ON B.CUSTCD               = D.CustCd 
					INNER JOIN MISTO..TOAsPart E
					   ON A.AsNo = E.AsNo 
					   AND A.Seq = E.Seq 
					WHERE A.SubmitYn             = 'Y'           --청구여부가 'Y' 인 건
					  AND A.SubmitYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND C.CustCD = @iCustCd 					
					  AND E.SupAmt  > 0
					GROUP BY A.SubmitYmd,C.ITEMGRPCD ,C.CustCd,B.EquipCustCD,B.SiteCD,B.DeliveryPlace,C.LotNo
		   		/*공구분실*/
				UNION ALL
					SELECT A.LOSSYMD
						,  A.ITEMGRPCD +  '_공구분실'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(B.CustCd)	AS CUSTNM
						,	MISTW.DBO.fn_gtcEquipNm(B.EquipCustCD) AS EQUIPCUSTNM
						,	MISTW.DBO.fn_gtcSiteNm(B.SiteCD)         AS SITE
						,	MISTW.DBO.fn_gtcDpNm(B.DeliveryPlace)  AS DeliveryPlace
						,	''  AS TELNO 
						,	MISTW.DBO.fn_gtcToolNm('2',A.ITEMGRPCD,A.LotNo)     AS ItemNm
						,	MISTW.DBO.fn_gtcToolSpec('2',A.ITEMGRPCD,A.LotNo)   AS Spec
						,	1              AS OutQty
						,	0              AS SalesPrice
						, A.Amt                 AS SUPPLYAMT --공급가액
						, A.Amt * 0.1           AS VAT       --부가세
						, A.Amt + A.Amt * 0.1   AS AMT       --금액                    
						, A.LotNo               AS ItemNo
   			  			, C.CustCd              AS CUSTCD
						, 1 AS ORD             --정렬순번(H)
				     	, 4 AS ORD2             --정렬순번(H)			  
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
					   ON A.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust C
					   ON B.CUSTCD               = C.CustCd 				
					WHERE A.LossYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.AMT                  > 0		
					  AND B.CustCd = @iCustCd 			  										
				/*공구분실취소*/
				UNION ALL
					SELECT A.CancelYmd 
						,  A.ITEMGRPCD +  '_공구분실취소'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(B.CustCd)	AS CUSTNM
						,	MISTW.DBO.fn_gtcEquipNm(B.EquipCustCD) AS EQUIPCUSTNM
						,	MISTW.DBO.fn_gtcSiteNm(B.SiteCD)         AS SITE
						,	MISTW.DBO.fn_gtcDpNm(B.DeliveryPlace)  AS DeliveryPlace
						,	''  AS TELNO 
						,	MISTW.DBO.fn_gtcToolNm('2',A.ITEMGRPCD,A.LotNo)     AS ItemNm
						,	MISTW.DBO.fn_gtcToolSpec('2',A.ITEMGRPCD,A.LotNo)   AS Spec
						,	1              AS OutQty
						,	0              AS SalesPrice
						, A.Amt * -1                 AS SUPPLYAMT --공급가액
						, A.Amt * 0.1 * -1           AS VAT       --부가세
						, (A.Amt*-1) + (A.Amt * 0.1 * -1)   AS AMT       --금액                    
						, A.LotNo               AS ItemNo
   			  			, C.CustCd              AS CUSTCD
						, 1 AS ORD             --정렬순번(H)
					    , 6 AS ORD2             --정렬순번(H)			   
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
					   ON A.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust C
					   ON B.CUSTCD               = C.CustCd 
					WHERE A.CancelYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.AMT                  > 0		
					  AND B.CustCd = @iCustCd 
				/*기타청구*/
				UNION ALL		    
				SELECT A.DELIVERYYMD
						,  A.ITEMGRPCD +  '_기타청구'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
						,	MISTW.DBO.fn_gtcEquipNm(A.EquipCustCD) AS EQUIPCUSTNM
						,	MISTW.DBO.fn_gtcSiteNm(A.SiteCD)         AS SITE
						,	MISTW.DBO.fn_gtcDpNm(A.DeliveryPlace)  AS DeliveryPlace
						,	''  AS TELNO 
						,	C.ItemDNm      AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, C.SUPPLYAMT      AS SUPPLYAMT --공급가액
						, C.VAT            AS VAT       --부가세
						, C.Amt            AS AMT       --금액
						, ''                    AS ItemNo
   			  			, A.CustCd              AS CUSTCD
						, 1 AS ORD             --정렬순번(H)
					    , 8 AS ORD2             --정렬순번(H)			   
				FROM MISTO..tbGtcTrade A
				INNER JOIN MISSA..SACust B
				  ON A.CustCd = B.CustCd 
				INNER JOIN MISTO..tbGtcTradeDetail C
				  ON C.TradeMgmNo = A.TradeMgmNo 
				WHERE A.CustCd = @iCustCd
				  AND A.DELIVERYYMD BETWEEN @w_OutYmdFr AND @iOutYmdTo			  
				/*공구화물비*/
				UNION ALL	   		    
				SELECT	MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2))           --패킹일자
					, A.ITEMGRPCD +  '_공구화물비'
					,	''  AS DELIVERYNO  --출고번호
					,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO      
					,	'공구화물비'       AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice 
					,	SUM(ISNULL(B.TransitFee,0))   AS SUPPLYAMT
					,	SUM(ISNULL(B.TransitVat,0))   AS VAT
					,	SUM(ISNULL(B.TransitSum,0))   AS AMT
					,	''             AS ItemNo
					,	A.CustCd       AS CUSTCD
					, 2 AS ORD             --정렬순번(H)
					, 0 AS ORD2             --정렬순번(H)
				FROM MISTW..tbGtcOrdEnr A
				JOIN MISTW..tbGtcPkgEnr B ON B.OrdEnrNo = A.OrdEnrNo
				 AND MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) BETWEEN @w_OutYmdFr AND @iOutYmdTo 
				WHERE 1=1 
				AND A.CustCd = @iCustCd
				AND A.OutStat ='04'
				GROUP BY MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) , A.CustCd,A.ITEMGRPCD
				HAVING SUM(ISNULL(B.TransitSum,0)) <> 0
				 ) X
			WHERE (@iOrdGb = '1' AND x.OrdGb = 'SP01')
				OR (@iOrdGb = '2' AND x.OrdGb = 'GP01')
				OR (@iOrdGb = '3' AND x.OrdGb IN ('SP02','SP03','SP04','SP05','SP06' ))
				OR (@iOrdGb = '4' AND x.OrdGb IN ('GP02','GP03','GP04','GP05','GP06' ))
				OR  (@iOrdGb = '%' AND x.OrdGb lIKE '%')
		
		UNION ALL --출고번호소계
			SELECT     X.OutYmd  AS OutYmd  --출고일자
					 , '출고번호 소계'  AS OrdGB --주문유형
					 , X.OutNo+' '+'출고번호 소계'      AS OutNo   --출고번호
					 , ''      AS CustNm  --거래처명	 
					 , ''      AS EquipCustNm --설비업체
					 , ''      AS Site        --현장
					 , ''      AS DeliveryPlace --화물지점
					 , ''      AS TelNo         --전화번호
					 , ''      AS ItemNm        --품목명
					 , ''      AS Spec          --규격
					 , 0       AS OutQty        --출고수량
					 , 0       AS SalesPrice    --단가
					 , SUM(X.SupplyAmt) AS SupplyAmt     --공급가액
					 , SUM(X.Vat)       AS Vat    --부가세
					 , SUM(X.Amt)       AS Amt    --합계
					 , ''      AS ItemNo        --품목코드(H)
					 , ''      AS CustCd        --거래처코드(H)
					 , ORD             --정렬순번(H)
					 , ORD2            --정렬순번(H)
				 FROM
				(SELECT 
					  A.OutYmd  --출고일자
					 , A.ITEMGRPCD + '_제품'   AS ORDGB
					 , A.OutNo   --출고번호
					 , D.CustNm  --거래처명	 
					 , A.EquipCustNm --설비업체
					 , A.Adr AS SITE   --현장
					 , A.DeliveryPlace --화물지점
					 , A.TelNo         --전화번호
					 , E.ItemNm        --품목명
					 , E.Spec          --규격
					 , B.OutQty        --출고수량
					 , B.SalesPrice    --단가
					 , B.SupplyAmt     --공급가액
					 , B.Vat           --부가세
					 , B.Amt           --합계
					 , B.ItemNo        --품목코드
					 , A.CustCd        --거래처코드
					 , 0 AS ORD             --정렬순번(H)
					 , 0 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbLogOut A
				  INNER JOIN MISTW..tbLogOutDtl B
				  ON A.OutEnrNo = B.OutEnrNo 
				  INNER JOIN MISSA..SACust D
				  ON A.CustCd  = D.CustCd 
				  INNER JOIN MISPD..PDItemMaster E
				  ON B.ITEMNO = E.ITEMNO			  
			   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
				 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				 --AND E.AcctGb <>'22'      -- 계정이 운반비 인것만
				 AND B.ItemNo NOT IN ('XS1-HM01','5XS1-HM02')
				 AND A.CustCd = @iCustCd
				--물류화물비(일별합산)
				UNION ALL
				SELECT 
					  A.OutYmd    --출고일자
					 , A.ItemGrpCd+'_물류화물비'          --주문유형
					 , ''           --출고번호
					 ,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명
					 , ''                                   AS EQUIPCUSTNM            --설비업체
					 , ''            --현장
					 , ''            --화물지점
					 , ''            --전화번호
					 , A.ItemGrpCd+'_물류화물비'        --품목명
					 , ''              --규격
					 , 0               --출고수량
					 , 0               --단가
					 , SUM(ISNULL(B.SupplyAmt,0)) AS SUPPLYAMT     --공급가액
					 , SUM(ISNULL(B.Vat,0))       AS VAT    --부가세
					 , SUM(ISNULL(B.Amt,0))       AS AMT    --합계
					 , ''              --품목코드
					 , A.CustCd        --거래처코드
					 , 0 AS ORD             --정렬순번(H)
					 , 3 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbLogOut A
				  INNER JOIN MISTW..tbLogOutDtl B
				  ON A.OutEnrNo = B.OutEnrNo 
				  INNER JOIN MISSA..SACust D
				  ON A.CustCd  = D.CustCd 
				  INNER JOIN MISPD..PDItemMaster E
				  ON B.ITEMNO = E.ITEMNO			  
			   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo				 
				 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				 --AND E.AcctGb ='22'      -- 계정이 운반비 인것만
				 AND B.ItemNo IN ('XS1-HM01','5XS1-HM02')
				 AND A.CustCd = @iCustCd
			   GROUP BY A.OutYmd ,A.CustCd,A.ITEMGRPCD
			   HAVING SUM(ISNULL(B.Amt,0)) <> 0
				UNION ALL
				--공구 판매
				SELECT	DD.IssueYmd
					 , A.ItemGrpCd+'_공구판매'          --주문유형
					 ,	DD.DeliveryNo
					 ,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					 ,	A.EquipCustNm                       AS EquipCustNm
					 ,	A.Site
					 ,	A.DeliveryPlace
					 ,	A.TelNo
					 ,	C.ClassNM
					 ,	C.Spec
					 ,	DD.qty
					 ,	DD.Price
					 ,	DD.SupAmt
					 ,	DD.Vat
					 ,	DD.Amt
					 ,	DD.LotNo
					 ,	A.CustCd
					 , 0 AS ORD             --정렬순번(H)
					 , 5 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbGtcOrdEnr A
				  JOIN MISTW..tbGtcOrdEnrDtl B ON B.OrdEnrNo = A.OrdEnrNo
				  JOIN MISTW..tbGtcSaleOut X
						ON B.OrdEnrDtlNo = X.OrdEnrDtlNo 
				  JOIN MISTO..ToDeliveryDetail DD 
						ON	DD.DeliveryNo = X.DeliveryNo 
						AND DD.Seq        = X.Seq					
				  JOIN MISTO..TOClass C ON C.ClassCd = DD.ClassCD AND C.itemGrpcd = DD.ItemGrpCd
				  JOIN MISTO..TODeliveryMaster E
				        ON DD.DeliveryNo = E.DeliveryNo 
				 WHERE A.CustCd = @iCustCd
				   AND A.OrdGb IN ('SP02', 'GP02')
				   AND E.DeliveryYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
				UNION ALL
				/*SP공구임대료*/
				SELECT  C.ThisMonDeadYmd
					, A.ItemGrpCd+'_임대료' 
					, ''
					, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					, A.EQUIPCUSTNM                     AS EquipCustNm
					, A.SITENM 
					, ''
					, ''
					, ''
					, ''
					, ''
					, ''			
					, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
					, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
					, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
					, ''
					, A.CUSTCD
					, 0 AS ORD             --정렬순번(H)
					, 7 AS ORD2            --정렬순번(H)
				FROM MISTO..TORentCalcHistory A
				INNER JOIN MISSA..SACust B
					ON A.CUSTCD = B.CustCd 
				INNER JOIN MISTO..TOCustDeadYmdMgm C
					ON A.CUSTCD = C.CustCd 
					AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					AND C.CalYm     = A.STANDARDYM
					AND C.ItemGrpCd ='SP'					
				WHERE 	A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)											
				  AND A.CUSTCD = @iCustCd		
				GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
				HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0
				UNION ALL
				/*GP공구임대료*/
				SELECT  C.ThisMonDeadYmd
					, A.ItemGrpCd+'_임대료' 
					, ''
					, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					, A.EQUIPCUSTNM                     AS EquipCustNm
					, A.SITENM 
					, ''
					, ''
					, ''
					, ''
					, ''
					, ''			
					, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
					, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
					, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
					, ''
					, A.CUSTCD
					, 0 AS ORD             --정렬순번(H)
					, 9 AS ORD2            --정렬순번(H)
				FROM MISTO..TORentCalcHistoryGP A
				INNER JOIN MISSA..SACust B
					ON A.CUSTCD = B.CustCd 
				INNER JOIN MISTO..TOCustDeadYmdMgm C
					ON A.CUSTCD = C.CustCd 
					AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					AND C.CalYm     = A.STANDARDYM
					AND C.ItemGrpCd ='GP'				
				WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)												
				  AND A.CUSTCD = @iCustCd	
				GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
				HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0		   
				/*AS연체료*/
				UNION ALL
				SELECT A.CALCYMD
					, A.ItemGrpCd+'_AS연체료' 
					,	''  AS DELIVERYNO  --출고번호
					,	C.CustNm 	AS CUSTNM      --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO 
					,	A.ItemGrpCd+'_AS연체료'     AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice 
					, SUM(B.RealLateFee)                            AS SUPPLYAMT --공급가액
					, SUM(B.RealLateFee) * 0.1                      AS VAT       --부가세
					, SUM(B.RealLateFee) + SUM(B.RealLateFee) * 0.1 AS AMT       --금액
					,	''             AS ItemNo
					, A.CustCd       AS CUSTCD
					, 1 AS ORD             --정렬순번(H)	
					, 1 AS ORD2            --정렬순번(H)			  
				FROM MISTO..tbASLate A
				INNER JOIN MISTO..tbASLateDtl B
				   ON A.AsLateMgmNo = B.AsLateMgmNo 
				INNER JOIN MISSA..SACust C
				   ON A.CUSTCD    = C.CustCd 
				WHERE 1=1 --A.CalcYm    = LEFT(@iOutYmdTo,6)					
				  AND A.CALCYMD   BETWEEN @w_OutYmdFr AND @iOutYmdTo
				  AND A.CustCd = @iCustCd 			  
				GROUP BY A.CUSTCD,C.CustNm,A.CALCYMD,A.ItemGrpCd
				UNION ALL
				/*AS청구비*/
				SELECT A.SubmitYmd
					,  C.ITEMGRPCD +  '_AS청구비'
					,	''  AS DELIVERYNO  --출고번호
					,	MISTW.DBO.fn_comCustNm(C.CustCd)	AS CUSTNM --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO 
					,	C.ItemGrpCd+'_AS청구비'     AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice
					, SUM(ISNULL(E.SupAmt,0))         AS SUPPLYAMT --공급가액
					, SUM(ISNULL(E.SupAmt,0)) * 0.1   AS VAT       --부가세
					, SUM(ISNULL(E.SupAmt,0)) + SUM(ISNULL(E.SupAmt,0)) * 0.1            AS AMT       --금액
					,	''             AS ItemNo
					, C.CustCd       AS CUSTCD
					, 1 AS ORD             --정렬순번(H)
					, 3 AS ORD2            --정렬순번(H)				  
					FROM MISTO..TOAsFault  A
					INNER JOIN MISTO..TOAs C
					   ON A.AsNo = C.AsNo 
					INNER JOIN MISTO..TODeliveryMaster B
					   ON C.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust D
					   ON B.CUSTCD               = D.CustCd 
					INNER JOIN MISTO..TOAsPart E
					   ON A.AsNo = E.AsNo 
					   AND A.Seq = E.Seq 
					WHERE A.SubmitYn             = 'Y'           --청구여부가 'Y' 인 건
					  AND A.SubmitYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND C.CustCd = @iCustCd 			  
					GROUP BY A.SubmitYmd,C.CUSTCD,C.ItemGrpCd
					HAVING SUM(E.SupAmt)  > 0

				/*공구분실*/
				UNION ALL
					SELECT A.LOSSYMD
						,  A.ITEMGRPCD +  '_공구분실'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(B.CustCd)	AS CUSTNM --거래처명
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_공구분실'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, SUM(A.Amt)                 AS SUPPLYAMT --공급가액
						, SUM(A.Amt) * 0.1           AS VAT       --부가세
						, SUM(A.Amt) + (SUM(A.Amt) * 0.1)   AS AMT       --금액                    
						, ''                    AS ItemNo
   			  			, B.CustCd              AS CUSTCD
						, 1 AS ORD             --정렬순번(H)
						, 5 AS ORD2            --정렬순번(H)				  
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
					   ON A.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust C
					   ON B.CUSTCD               = C.CustCd 
					WHERE A.LossYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.AMT                  > 0		
					  AND B.CustCd = @iCustCd
					GROUP BY A.LOSSYMD,B.CUSTCD,A.ItemGrpCd				   			  										
				/*공구분실취소*/
				UNION ALL
					SELECT A.CancelYmd 
						,  A.ITEMGRPCD +  '_공구분실취소'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(B.CustCd)	AS CUSTNM --거래처명	 
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_공구분실취소'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, SUM(A.Amt) * -1                AS SUPPLYAMT --공급가액
						, SUM(A.Amt) * 0.1 * -1           AS VAT       --부가세
						, (SUM(A.Amt) + (SUM(A.Amt) * 0.1))*-1   AS AMT       --금액                                    
						, ''                    AS ItemNo
   			  			, B.CustCd              AS CUSTCD
						, 1 AS ORD             --정렬순번(H)	
						, 7 AS ORD2            --정렬순번(H)			  
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
					   ON A.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust C
					   ON B.CUSTCD               = C.CustCd 
					WHERE A.CancelYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.AMT                  > 0		
					  AND B.CustCd = @iCustCd 
					GROUP BY A.CancelYmd ,B.CUSTCD,A.ItemGrpCd				   			  										
			
				/*기타청구*/
				UNION ALL		    
				SELECT A.DELIVERYYMD
						,  A.ITEMGRPCD +  '_기타청구'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_기타청구'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, SUM(ISNULL(C.SUPPLYAMT,0))  AS SUPPLYAMT --공급가액
						, SUM(ISNULL(C.VAT,0))        AS VAT       --부가세
						, SUM(ISNULL(C.Amt,0))        AS AMT       --금액
						, ''                    AS ItemNo
   			  			, A.CustCd              AS CUSTCD
						, 1 AS ORD             --정렬순번(H)	
						, 9 AS ORD2            --정렬순번(H)			  										
				FROM MISTO..tbGtcTrade A
				INNER JOIN MISSA..SACust B
				  ON A.CustCd = B.CustCd 
				INNER JOIN MISTO..tbGtcTradeDetail C
				  ON C.TradeMgmNo = A.TradeMgmNo 
				WHERE A.CustCd = @iCustCd
				  AND A.DELIVERYYMD BETWEEN @w_OutYmdFr AND @iOutYmdTo
				GROUP BY A.DELIVERYYMD ,A.CUSTCD,A.ItemGrpCd
				/*공구화물비*/
				UNION ALL	   		    
				SELECT	MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2))          --패킹일자
					,	A.ItemGrpCd+'_공구화물비'          --주문유형           --주문유형
					,	''  AS DELIVERYNO  --출고번호
					,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO      
					,	A.ItemGrpCd+'_공구화물비'       AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice 
					,	SUM(ISNULL(B.TransitFee,0))   AS SUPPLYAMT
					,	SUM(ISNULL(B.TransitVat,0))   AS VAT
					,	SUM(ISNULL(B.TransitSum,0))   AS AMT
					,	''             AS ItemNo
					,	A.CustCd       AS CUSTCD
					, 2 AS ORD             --정렬순번(H)
					, 1 AS ORD2            --정렬순번(H)
				FROM MISTW..tbGtcOrdEnr A
				JOIN MISTW..tbGtcPkgEnr B ON B.OrdEnrNo = A.OrdEnrNo
				 AND MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) BETWEEN @w_OutYmdFr AND @iOutYmdTo 
				WHERE 1=1 
				AND A.CustCd = @iCustCd
				AND A.OutStat ='04'
				GROUP BY MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) , A.CustCd,A.ITEMGRPCD
				HAVING SUM(ISNULL(B.TransitSum,0)) > 0
				 ) X
			WHERE (@iOrdGb = '1' AND x.OrdGb = 'SP01')
				OR (@iOrdGb = '2' AND x.OrdGb = 'GP01')
				OR (@iOrdGb = '3' AND x.OrdGb IN ('SP02','SP03','SP04','SP05','SP06' ))
				OR (@iOrdGb = '4' AND x.OrdGb IN ('GP02','GP03','GP04','GP05','GP06' ))
				OR  (@iOrdGb = '%' AND x.OrdGb lIKE '%')
			GROUP BY X.OUTYMD,X.OutNo,X.ORD,X.ORD2
			--GROUP BY X.OUTYMD,X.ORDGB,X.OutNo,X.ORD,X.ORD2

			UNION ALL --일별소계
			SELECT     X.OutYmd  AS OutYmd  --출고일자
					 , '일별 소계'  AS OrdGB --주문유형
					 , ''      AS OutNo   --출고번호
					 , ''      AS CustNm  --거래처명	 
					 , ''      AS EquipCustNm --설비업체
					 , ''      AS Site        --현장
					 , ''      AS DeliveryPlace --화물지점
					 , ''      AS TelNo         --전화번호
					 , ''      AS ItemNm        --품목명
					 , ''      AS Spec          --규격
					 , 0       AS OutQty        --출고수량
					 , 0       AS SalesPrice    --단가
					 , SUM(X.SupplyAmt) AS SupplyAmt     --공급가액
					 , SUM(X.Vat)       AS Vat    --부가세
					 , SUM(X.Amt)       AS Amt    --합계
					 , ''      AS ItemNo        --품목코드(H)
					 , ''      AS CustCd        --거래처코드(H)
					 , ORD             --정렬순번(H)
					 , ORD2            --정렬순번(H)
				 FROM
				(SELECT 
					  A.OutYmd  --출고일자
					 , A.ITEMGRPCD + '_제품'   AS ORDGB
					 , A.OutNo   --출고번호
					 , D.CustNm  --거래처명	 
					 , A.EquipCustNm --설비업체
					 , A.Adr AS SITE   --현장
					 , A.DeliveryPlace --화물지점
					 , A.TelNo         --전화번호
					 , E.ItemNm        --품목명
					 , E.Spec          --규격
					 , B.OutQty        --출고수량
					 , B.SalesPrice    --단가
					 , B.SupplyAmt     --공급가액
					 , B.Vat           --부가세
					 , B.Amt           --합계
					 , B.ItemNo        --품목코드
					 , A.CustCd        --거래처코드
					 , 3 AS ORD             --정렬순번(H)
					 , 0 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbLogOut A
				  INNER JOIN MISTW..tbLogOutDtl B
				  ON A.OutEnrNo = B.OutEnrNo 
				  INNER JOIN MISSA..SACust D
				  ON A.CustCd  = D.CustCd 
				  INNER JOIN MISPD..PDItemMaster E
				  ON B.ITEMNO = E.ITEMNO			  
			   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
				 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				 --AND E.AcctGb <>'22'      -- 계정이 운반비 인것만
				 AND B.ItemNo NOT IN ('XS1-HM01','5XS1-HM02')
				 AND A.CustCd = @iCustCd
				--물류화물비(일별합산)
				UNION ALL
				SELECT 
					  A.OutYmd    --출고일자
					 , A.ItemGrpCd+'_물류화물비'          --주문유형
					 , ''           --출고번호
					 ,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명
					 , ''                                   AS EQUIPCUSTNM            --설비업체
					 , ''            --현장
					 , ''            --화물지점
					 , ''            --전화번호
					 , A.ItemGrpCd+'_물류화물비'        --품목명
					 , ''              --규격
					 , 0               --출고수량
					 , 0               --단가
					 , SUM(ISNULL(B.SupplyAmt,0)) AS SUPPLYAMT     --공급가액
					 , SUM(ISNULL(B.Vat,0))       AS VAT    --부가세
					 , SUM(ISNULL(B.Amt,0))       AS AMT    --합계
					 , ''              --품목코드
					 , A.CustCd        --거래처코드
					 , 3 AS ORD             --정렬순번(H)
					 , 0 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbLogOut A
				  INNER JOIN MISTW..tbLogOutDtl B
				  ON A.OutEnrNo = B.OutEnrNo 
				  INNER JOIN MISSA..SACust D
				  ON A.CustCd  = D.CustCd 
				  INNER JOIN MISPD..PDItemMaster E
				  ON B.ITEMNO = E.ITEMNO			  
			   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo				 
				 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				 --AND E.AcctGb ='22'      -- 계정이 운반비 인것만
				 AND B.ItemNo IN ('XS1-HM01','5XS1-HM02')
				 AND A.CustCd = @iCustCd
			   GROUP BY A.OutYmd ,A.CustCd,A.ITEMGRPCD
			   HAVING SUM(ISNULL(B.Amt,0)) <> 0
				UNION ALL
				--공구 판매
				SELECT	DD.IssueYmd
					 , A.ItemGrpCd+'_공구판매'          --주문유형
					 ,	DD.DeliveryNo
					 ,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					 ,	A.EquipCustNm                       AS EquipCustNm
					 ,	A.Site
					 ,	A.DeliveryPlace
					 ,	A.TelNo
					 ,	C.ClassNM
					 ,	C.Spec
					 ,	DD.qty
					 ,	DD.Price
					 ,	DD.SupAmt
					 ,	DD.Vat
					 ,	DD.Amt
					 ,	DD.LotNo
					 ,	A.CustCd
					 , 3 AS ORD             --정렬순번(H)
					 , 0 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbGtcOrdEnr A
				  JOIN MISTW..tbGtcOrdEnrDtl B ON B.OrdEnrNo = A.OrdEnrNo
				  JOIN MISTW..tbGtcSaleOut X
						ON B.OrdEnrDtlNo = X.OrdEnrDtlNo 
				  JOIN MISTO..ToDeliveryDetail DD 
						ON	DD.DeliveryNo = X.DeliveryNo 
						AND DD.Seq        = X.Seq					
				  JOIN MISTO..TOClass C ON C.ClassCd = DD.ClassCD AND C.itemGrpcd = DD.ItemGrpCd
				  JOIN MISTO..TODeliveryMaster E
				        ON DD.DeliveryNo = E.DeliveryNo 
				 WHERE A.CustCd = @iCustCd
				   AND A.OrdGb IN ('SP02', 'GP02')
				   AND E.DeliveryYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
				UNION ALL
				/*SP공구임대료*/
				SELECT  C.ThisMonDeadYmd
					, A.ItemGrpCd+'_임대료' 
					, ''
					, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					, A.EQUIPCUSTNM                     AS EquipCustNm
					, A.SITENM 
					, ''
					, ''
					, ''
					, ''
					, ''
					, ''			
					, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
					, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
					, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
					, ''
					, A.CUSTCD
					, 3 AS ORD             --정렬순번(H)
					, 0 AS ORD2            --정렬순번(H)
				FROM MISTO..TORentCalcHistory A
				INNER JOIN MISSA..SACust B
					ON A.CUSTCD = B.CustCd 
				INNER JOIN MISTO..TOCustDeadYmdMgm C
					ON A.CUSTCD = C.CustCd 
					AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					AND C.CalYm     = A.STANDARDYM
					AND C.ItemGrpCd ='SP'					
				WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)											
				  AND A.CUSTCD = @iCustCd		
				GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
				HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0
				UNION ALL
				/*GP공구임대료*/
				SELECT  C.ThisMonDeadYmd
					, A.ItemGrpCd+'_임대료' 
					, ''
					, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					, A.EQUIPCUSTNM                     AS EquipCustNm
					, A.SITENM 
					, ''
					, ''
					, ''
					, ''
					, ''
					, ''			
					, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
					, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
					, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
					, ''
					, A.CUSTCD
					, 3 AS ORD             --정렬순번(H)
					, 0 AS ORD2            --정렬순번(H)
				FROM MISTO..TORentCalcHistoryGP A
				INNER JOIN MISSA..SACust B
					ON A.CUSTCD = B.CustCd 
				INNER JOIN MISTO..TOCustDeadYmdMgm C
					ON A.CUSTCD = C.CustCd 
					AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					AND C.CalYm     = A.STANDARDYM
					AND C.ItemGrpCd ='GP'				
				WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)											
				  AND A.CUSTCD = @iCustCd	
				GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
				HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0		   
				/*AS연체료*/
				UNION ALL
				SELECT A.CALCYMD
					, A.ItemGrpCd+'_AS연체료' 
					,	''  AS DELIVERYNO  --출고번호
					,	C.CustNm 	AS CUSTNM      --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO 
					,	A.ItemGrpCd+'_AS연체료'     AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice 
					, SUM(B.RealLateFee)                            AS SUPPLYAMT --공급가액
					, SUM(B.RealLateFee) * 0.1                      AS VAT       --부가세
					, SUM(B.RealLateFee) + SUM(B.RealLateFee) * 0.1 AS AMT       --금액
					,	''             AS ItemNo
					, A.CustCd       AS CUSTCD
					, 3 AS ORD             --정렬순번(H)	
					, 0 AS ORD2            --정렬순번(H)			  
				FROM MISTO..tbASLate A
				INNER JOIN MISTO..tbASLateDtl B
				   ON A.AsLateMgmNo = B.AsLateMgmNo 
				INNER JOIN MISSA..SACust C
				   ON A.CUSTCD    = C.CustCd 
				WHERE 1=1 --A.CalcYm    = LEFT(@iOutYmdTo,6)					
				  AND A.CALCYMD   BETWEEN @w_OutYmdFr AND @iOutYmdTo
				  AND A.CustCd = @iCustCd 			  
				GROUP BY A.CUSTCD,C.CustNm,A.CALCYMD,A.ItemGrpCd
				UNION ALL
				/*AS청구비*/
				SELECT A.SubmitYmd
					,  C.ITEMGRPCD +  '_AS청구비'
					,	''  AS DELIVERYNO  --출고번호
					,	MISTW.DBO.fn_comCustNm(C.CustCd)	AS CUSTNM --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO 
					,	C.ItemGrpCd+'_AS청구비'     AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice
					, SUM(ISNULL(E.SupAmt,0))         AS SUPPLYAMT --공급가액
					, SUM(ISNULL(E.SupAmt,0)) * 0.1   AS VAT       --부가세
					, SUM(ISNULL(E.SupAmt,0)) + SUM(ISNULL(E.SupAmt,0)) * 0.1            AS AMT       --금액
					,	''             AS ItemNo
					, C.CustCd       AS CUSTCD
					, 3 AS ORD             --정렬순번(H)
					, 0 AS ORD2            --정렬순번(H)				  
					FROM MISTO..TOAsFault  A
					INNER JOIN MISTO..TOAs C
					   ON A.AsNo = C.AsNo 
					INNER JOIN MISTO..TODeliveryMaster B
					   ON C.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust D
					   ON B.CUSTCD               = D.CustCd 
					INNER JOIN MISTO..TOAsPart E
					   ON A.AsNo = E.AsNo 
					   AND A.Seq = E.Seq 
					WHERE A.SubmitYn             = 'Y'           --청구여부가 'Y' 인 건
					  AND A.SubmitYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND C.CustCd = @iCustCd 			  
					GROUP BY A.SubmitYmd,C.CUSTCD,C.ItemGrpCd
					HAVING SUM(E.SupAmt)  > 0

				/*공구분실*/
				UNION ALL
					SELECT A.LOSSYMD
						,  A.ITEMGRPCD +  '_공구분실'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(B.CustCd)	AS CUSTNM --거래처명
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_공구분실'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, SUM(A.Amt)                 AS SUPPLYAMT --공급가액
						, SUM(A.Amt) * 0.1           AS VAT       --부가세
						, SUM(A.Amt) + (SUM(A.Amt) * 0.1)   AS AMT       --금액                    
						, ''                    AS ItemNo
   			  			, B.CustCd              AS CUSTCD
						, 3 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)				  
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
					   ON A.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust C
					   ON B.CUSTCD               = C.CustCd 
					WHERE A.LossYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.AMT                  > 0		
					  AND B.CustCd = @iCustCd
					GROUP BY A.LOSSYMD,B.CUSTCD,A.ItemGrpCd				   			  										
				/*공구분실취소*/
				UNION ALL
					SELECT A.CancelYmd 
						,  A.ITEMGRPCD +  '_공구분실취소'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(B.CustCd)	AS CUSTNM --거래처명	 
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_공구분실취소'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, SUM(A.Amt) * -1                AS SUPPLYAMT --공급가액
						, SUM(A.Amt) * 0.1 * -1           AS VAT       --부가세
						, (SUM(A.Amt) + (SUM(A.Amt) * 0.1))*-1   AS AMT       --금액                                    
						, ''                    AS ItemNo
   			  			, B.CustCd              AS CUSTCD
						, 3 AS ORD             --정렬순번(H)	
						, 0 AS ORD2            --정렬순번(H)			  
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
					   ON A.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust C
					   ON B.CUSTCD               = C.CustCd 
					WHERE A.CancelYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.AMT                  > 0		
					  AND B.CustCd = @iCustCd 
					GROUP BY A.CancelYmd ,B.CUSTCD,A.ItemGrpCd				   			  										
			
				/*기타청구*/
				UNION ALL		    
				SELECT A.DELIVERYYMD
						,  A.ITEMGRPCD +  '_기타청구'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_기타청구'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, SUM(ISNULL(C.SUPPLYAMT,0))  AS SUPPLYAMT --공급가액
						, SUM(ISNULL(C.VAT,0))        AS VAT       --부가세
						, SUM(ISNULL(C.Amt,0))        AS AMT       --금액
						, ''                    AS ItemNo
   			  			, A.CustCd              AS CUSTCD
						, 3 AS ORD             --정렬순번(H)	
						, 0 AS ORD2            --정렬순번(H)			  										
				FROM MISTO..tbGtcTrade A
				INNER JOIN MISSA..SACust B
				  ON A.CustCd = B.CustCd 
				INNER JOIN MISTO..tbGtcTradeDetail C
				  ON C.TradeMgmNo = A.TradeMgmNo 
				WHERE A.CustCd = @iCustCd
				  AND A.DELIVERYYMD BETWEEN @w_OutYmdFr AND @iOutYmdTo
				GROUP BY A.DELIVERYYMD ,A.CUSTCD,A.ItemGrpCd
				/*공구화물비*/
				UNION ALL	   		    
				SELECT	MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2))          --패킹일자
					,	A.ItemGrpCd+'_공구화물비'          --주문유형           --주문유형
					,	''  AS DELIVERYNO  --출고번호
					,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO      
					,	A.ItemGrpCd+'_공구화물비'       AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice 
					,	SUM(ISNULL(B.TransitFee,0))   AS SUPPLYAMT
					,	SUM(ISNULL(B.TransitVat,0))   AS VAT
					,	SUM(ISNULL(B.TransitSum,0))   AS AMT
					,	''             AS ItemNo
					,	A.CustCd       AS CUSTCD
					, 3 AS ORD             --정렬순번(H)
					, 0 AS ORD2            --정렬순번(H)
				FROM MISTW..tbGtcOrdEnr A
				JOIN MISTW..tbGtcPkgEnr B ON B.OrdEnrNo = A.OrdEnrNo
				 AND MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) BETWEEN @w_OutYmdFr AND @iOutYmdTo 
				WHERE 1=1 
				AND A.CustCd = @iCustCd
				AND A.OutStat ='04'
				GROUP BY MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) , A.CustCd,A.ITEMGRPCD
				HAVING SUM(ISNULL(B.TransitSum,0)) > 0
				 ) X
			WHERE (@iOrdGb = '1' AND x.OrdGb = 'SP01')
				OR (@iOrdGb = '2' AND x.OrdGb = 'GP01')
				OR (@iOrdGb = '3' AND x.OrdGb IN ('SP02','SP03','SP04','SP05','SP06' ))
				OR (@iOrdGb = '4' AND x.OrdGb IN ('GP02','GP03','GP04','GP05','GP06' ))
				OR  (@iOrdGb = '%' AND x.OrdGb lIKE '%')
			GROUP BY X.OUTYMD,X.ORD,X.ORD2

			UNION ALL --총계
			SELECT     @iOutYmdTo      AS OutYmd  --출고일자
					 , '총계'  AS OrdGB --주문유형
					 , ''      AS OutNo   --출고번호
					 , ''      AS CustNm  --거래처명	 
					 , ''      AS EquipCustNm --설비업체
					 , ''      AS Site        --현장
					 , ''      AS DeliveryPlace --화물지점
					 , ''      AS TelNo         --전화번호
					 , ''      AS ItemNm        --품목명
					 , ''      AS Spec          --규격
					 , 0       AS OutQty        --출고수량
					 , 0       AS SalesPrice    --단가
					 , SUM(X.SupplyAmt) AS SupplyAmt     --공급가액
					 , SUM(X.Vat)       AS Vat    --부가세
					 , SUM(X.Amt)       AS Amt    --합계
					 , ''      AS ItemNo        --품목코드(H)
					 , ''      AS CustCd        --거래처코드(H)
					 , ORD             --정렬순번(H)
					 , ORD2            --정렬순번(H)
				 FROM
				(SELECT 
					  A.OutYmd  --출고일자
					 , C.OrdGB --주문유형
					 , A.OutNo   --출고번호
					 , D.CustNm  --거래처명	 
					 , A.EquipCustNm --설비업체
					 , A.Site        --현장
					 , A.DeliveryPlace --화물지점
					 , A.TelNo         --전화번호
					 , E.ItemNm        --품목명
					 , E.Spec          --규격
					 , B.OutQty        --출고수량
					 , B.SalesPrice    --단가
					 , B.SupplyAmt     --공급가액
					 , B.Vat           --부가세
					 , B.Amt           --합계
					 , B.ItemNo        --품목코드
					 , A.CustCd        --거래처코드
					 , 4 AS ORD             --정렬순번(H)
					 , 0 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbLogOut A
				  INNER JOIN MISTW..tbLogOutDtl B
				  ON A.OutEnrNo = B.OutEnrNo 
				  INNER JOIN tbLogOrdEnr C
				  ON B.OrdEnrNo = C.OrdEnrNo  
				  INNER JOIN MISSA..SACust D
				  ON A.CustCd  = D.CustCd 
				  INNER JOIN MISPD..PDItemMaster E
				  ON B.ITEMNO = E.ITEMNO			  
			   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
				 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')				 
				 AND B.ItemNo NOT IN ('XS1-HM01','5XS1-HM02')
				 AND A.CustCd = @iCustCd
				--물류화물비(일별합산)
				UNION ALL
				SELECT 
					  A.OutYmd    --출고일자
					 , ''         --주문유형
					 , ''           --출고번호
					 , ''           --거래처명	 
					 , ''           --설비업체
					 , ''           --현장
					 , ''           --화물지점
					 , ''           --전화번호
					 , '화물비'        --품목명
					 , ''              --규격
					 , 0               --출고수량
					 , 0               --단가
					 , SUM(ISNULL(B.SupplyAmt,0)) AS SUPPLYAMT     --공급가액
					 , SUM(ISNULL(B.Vat,0))       AS VAT    --부가세
					 , SUM(ISNULL(B.Amt,0))       AS AMT    --합계
					 , ''              --품목코드
					 , A.CustCd        --거래처코드
					 , 4 AS ORD             --정렬순번(H)
					 , 0 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbLogOut A
				  INNER JOIN MISTW..tbLogOutDtl B
				  ON A.OutEnrNo = B.OutEnrNo 
				  INNER JOIN MISSA..SACust D
				  ON A.CustCd  = D.CustCd 
				  INNER JOIN MISPD..PDItemMaster E
				  ON B.ITEMNO = E.ITEMNO			  
			   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
				 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				 --AND E.AcctGb ='22'      -- 계정이 운반비 인것만
				 AND B.ItemNo IN ('XS1-HM01','5XS1-HM02')
				 AND A.CustCd = @iCustCd
			   GROUP BY A.OutYmd ,A.CustCd
			   HAVING SUM(ISNULL(B.Amt,0)) <> 0
				UNION ALL
				--공구 판매
				SELECT	DD.IssueYmd
					 ,	A.OrdGB
					 ,	DD.DeliveryNo
					 ,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					 ,	A.EquipCustNm
					 ,	A.Site
					 ,	A.DeliveryPlace
					 ,	A.TelNo
					 ,	C.ClassNM
					 ,	C.Spec
					 ,	DD.qty
					 ,	DD.Price
					 ,	DD.SupAmt
					 ,	DD.Vat
					 ,	DD.Amt
					 ,	DD.LotNo
					 ,	A.CustCd
					 , 4 AS ORD             --정렬순번(H)
					 , 0 AS ORD2            --정렬순번(H)
				  FROM MISTW..tbGtcOrdEnr A
				  JOIN MISTW..tbGtcOrdEnrDtl B ON B.OrdEnrNo = A.OrdEnrNo
				  JOIN MISTW..tbGtcSaleOut X
						ON B.OrdEnrDtlNo = X.OrdEnrDtlNo 
				  JOIN MISTO..ToDeliveryDetail DD 
						ON	DD.DeliveryNo = X.DeliveryNo 
						AND DD.Seq        = X.Seq					
				  JOIN MISTO..TOClass C ON C.ClassCd = DD.ClassCD AND C.itemGrpcd = DD.ItemGrpCd
				  JOIN MISTO..TODeliveryMaster E
				        ON DD.DeliveryNo = E.DeliveryNo 
				 WHERE A.CustCd = @iCustCd
				   AND A.OrdGb IN ('SP02', 'GP02')
				   AND E.DeliveryYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
				UNION ALL
				/*SP공구임대료*/
				SELECT  C.ThisMonDeadYmd
					, A.ItemGrpCd+'_임대료' 
					, ''
					, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					, A.EQUIPCUSTNM 
					, A.SITENM 
					, ''
					, ''
					, ''
					, ''
					, ''
					, ''			
					, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
					, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
					, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
					, ''
					, A.CustCd
					, 4 AS ORD             --정렬순번(H)
					, 0 AS ORD2            --정렬순번(H)
				FROM MISTO..TORentCalcHistory A
				INNER JOIN MISSA..SACust B
					ON A.CUSTCD = B.CustCd 
				INNER JOIN MISTO..TOCustDeadYmdMgm C
					ON A.CUSTCD = C.CustCd 
					AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					AND C.CalYm     = A.STANDARDYM	
					AND C.ItemGrpCd ='SP'				
				WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)											
				  AND A.CUSTCD = @iCustCd	
				GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
				HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0
				UNION ALL
				/*GP공구임대료*/
				SELECT  C.ThisMonDeadYmd
					, A.ItemGrpCd+'_임대료' 
					, ''
					, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
					, A.EQUIPCUSTNM 
					, A.SITENM 
					, ''
					, ''
					, ''
					, ''
					, ''
					, ''			
					, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
					, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
					, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
					, ''
					, A.CustCd
					, 4 AS ORD             --정렬순번(H)
					, 0 AS ORD2            --정렬순번(H)
				FROM MISTO..TORentCalcHistoryGP A
				INNER JOIN MISSA..SACust B
					ON A.CUSTCD = B.CustCd 
				INNER JOIN MISTO..TOCustDeadYmdMgm C
					ON A.CUSTCD = C.CustCd 
					AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					AND C.CalYm     = A.STANDARDYM
					AND C.ItemGrpCd ='GP'					
				WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)												
				  AND A.CUSTCD = @iCustCd	
				GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
				HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0		   
				/*AS연체료*/
				UNION ALL
				SELECT A.CALCYMD
					, A.ItemGrpCd+'_AS연체료'
					,	''  AS DELIVERYNO  --출고번호
					,	''	AS CUSTNM      --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO 
					,	A.ItemGrpCd+'_AS연체료'     AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice 
					, SUM(B.RealLateFee)                            AS SUPPLYAMT --공급가액
					, SUM(B.RealLateFee) * 0.1                      AS VAT       --부가세
					, SUM(B.RealLateFee) + SUM(B.RealLateFee) * 0.1 AS AMT       --금액
					,	''             AS ItemNo
					, A.CustCd       AS CUSTCD
					, 4 AS ORD             --정렬순번(H)	
					, 0 AS ORD2            --정렬순번(H)			  
				FROM MISTO..tbASLate A
				INNER JOIN MISTO..tbASLateDtl B
				   ON A.AsLateMgmNo = B.AsLateMgmNo 
				INNER JOIN MISSA..SACust C
				   ON A.CUSTCD    = C.CustCd 
				WHERE 1=1 --A.CalcYm    = LEFT(@iOutYmdTo,6)					
				  AND A.CALCYMD   BETWEEN @w_OutYmdFr AND @iOutYmdTo
				  AND A.CustCd = @iCustCd 			  
				GROUP BY A.CUSTCD,A.CALCYMD,A.ItemGrpCd
				UNION ALL
				/*AS청구비*/
				SELECT A.SubmitYmd
					,  C.ITEMGRPCD +  '_AS청구비'
					,	''  AS DELIVERYNO  --출고번호
					,	''	AS CUSTNM      --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO 
					,	C.ItemGrpCd+'_AS청구비'     AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice
					, SUM(ISNULL(E.SupAmt,0))         AS SUPPLYAMT --공급가액
					, SUM(ISNULL(E.SupAmt,0)) * 0.1   AS VAT       --부가세
					, SUM(ISNULL(E.SupAmt,0)) + SUM(ISNULL(E.SupAmt,0)) * 0.1            AS AMT       --금액
					,	''             AS ItemNo
					, C.CustCd       AS CUSTCD
					, 4 AS ORD             --정렬순번(H)	
					, 0 AS ORD2            --정렬순번(H)			  
					FROM MISTO..TOAsFault  A
					INNER JOIN MISTO..TOAs C
					   ON A.AsNo = C.AsNo 
					INNER JOIN MISTO..TODeliveryMaster B
					   ON C.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust D
					   ON B.CUSTCD               = D.CustCd 
					INNER JOIN MISTO..TOAsPart E
					   ON A.AsNo = E.AsNo 
					   AND A.Seq = E.Seq 
					WHERE A.SubmitYn             = 'Y'           --청구여부가 'Y' 인 건
					  AND A.SubmitYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND C.CustCd = @iCustCd 			  
					GROUP BY A.SubmitYmd,C.CUSTCD,C.ItemGrpCd
					HAVING SUM(E.SupAmt)  > 0
				/*공구분실*/
				UNION ALL
					SELECT A.LOSSYMD
						,  A.ITEMGRPCD +  '_공구분실'
						,	''  AS DELIVERYNO  --출고번호
						,	''	AS CUSTNM      --거래처명	 
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_공구분실'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, SUM(A.Amt)                 AS SUPPLYAMT --공급가액
						, SUM(A.Amt) * 0.1           AS VAT       --부가세
						, SUM(A.Amt) + (SUM(A.Amt) * 0.1)   AS AMT       --금액                    
						, ''                    AS ItemNo
   			  			, B.CustCd              AS CUSTCD
						, 4 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)				  
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
					   ON A.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust C
					   ON B.CUSTCD               = C.CustCd 
					WHERE A.LossYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.AMT                  > 0		
					  AND B.CustCd = @iCustCd
					GROUP BY A.LOSSYMD,B.CUSTCD,A.ItemGrpCd				   			  										
				/*공구분실취소*/
				UNION ALL
					SELECT A.CancelYmd 
						,  A.ITEMGRPCD +  '_공구분실취소'
						,	''  AS DELIVERYNO  --출고번호
						,	''	AS CUSTNM      --거래처명	 
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_공구분실취소'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, SUM(A.Amt) * -1                AS SUPPLYAMT --공급가액
						, SUM(A.Amt) * 0.1 * -1           AS VAT       --부가세
						, (SUM(A.Amt) + (SUM(A.Amt) * 0.1))*-1   AS AMT       --금액                                    
						, ''                    AS ItemNo
   			  			, B.CustCd              AS CUSTCD
						, 4 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)				  
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
					   ON A.DeliveryNo           = B.DeliveryNo 
					INNER JOIN MISSA..SACust C
					   ON B.CUSTCD               = C.CustCd 
					WHERE A.CancelYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.AMT                  > 0		
					  AND B.CustCd = @iCustCd 
					GROUP BY A.CancelYmd ,B.CUSTCD,A.ItemGrpCd	
				/*기타청구*/
				UNION ALL		    
				SELECT A.DELIVERYYMD
						,  A.ITEMGRPCD +  '_기타청구'
						,	''  AS DELIVERYNO  --출고번호
						,	''	AS CUSTNM      --거래처명	 
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_기타청구'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice
						, SUM(ISNULL(C.SUPPLYAMT,0))  AS SUPPLYAMT --공급가액
						, SUM(ISNULL(C.VAT,0))        AS VAT       --부가세
						, SUM(ISNULL(C.Amt,0))        AS AMT       --금액
						, ''                    AS ItemNo
   			  			, A.CustCd              AS CUSTCD
						, 4 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)				  										
				FROM MISTO..tbGtcTrade A
				INNER JOIN MISSA..SACust B
				  ON A.CustCd = B.CustCd 
				INNER JOIN MISTO..tbGtcTradeDetail C
				  ON C.TradeMgmNo = A.TradeMgmNo 
				WHERE A.CustCd = @iCustCd
				AND A.DELIVERYYMD BETWEEN @w_OutYmdFr AND @iOutYmdTo
				GROUP BY A.DELIVERYYMD ,A.CUSTCD,A.ItemGrpCd
				/*공구화물비*/
				UNION ALL	   		    
				SELECT	MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2))           --패킹일자
					,	A.ItemGrpCd+'_공구화물비'            --주문유형
					,	''  AS DELIVERYNO  --출고번호
					,	''	AS CUSTNM      --거래처명	 
					,	''  AS EQUIPCUSTNM
					,	''  AS SITE
					,	''  AS DELIVERYPLACE
					,	''  AS TELNO      
					,	A.ItemGrpCd+'_공구화물비'       AS ItemNm
					,	''             AS Spec
					,	0              AS OutQty
					,	0              AS SalesPrice 
					,	SUM(ISNULL(B.TransitFee,0))   AS SUPPLYAMT
					,	SUM(ISNULL(B.TransitVat,0))   AS VAT
					,	SUM(ISNULL(B.TransitSum,0))   AS AMT
					,	''             AS ItemNo
					,	A.CustCd       AS CUSTCD
					, 4 AS ORD             --정렬순번(H)
					, 0 AS ORD2            --정렬순번(H)
				FROM MISTW..tbGtcOrdEnr A
				JOIN MISTW..tbGtcPkgEnr B ON B.OrdEnrNo = A.OrdEnrNo
				 AND MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) BETWEEN @w_OutYmdFr AND @iOutYmdTo 
				WHERE 1=1 
				AND A.CustCd = @iCustCd
				AND A.OutStat ='04'
				GROUP BY MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) , A.CustCd,A.ItemGrpCd
				HAVING SUM(ISNULL(B.TransitSum,0)) > 0
				 ) X
			WHERE (@iOrdGb = '1' AND x.OrdGb = 'SP01')
				OR (@iOrdGb = '2' AND x.OrdGb = 'GP01')
				OR (@iOrdGb = '3' AND x.OrdGb IN ('SP02','SP03','SP04','SP05','SP06' ))
				OR (@iOrdGb = '4' AND x.OrdGb IN ('GP02','GP03','GP04','GP05','GP06' ))
				OR  (@iOrdGb = '%' AND x.OrdGb lIKE '%')
			GROUP BY X.ORD,X.ORD2  

			) X
			ORDER BY X.OUTYMD,X.ORD,X.ORD2,X.OutNo  ASC
		   
		   END  
	
	ELSE IF @iKind = '1' --소계안보이게 처리
		BEGIN	 	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   	   BEGIN 		
			   --물류출고건
				SELECT X.OutYmd  --출고일자
		   			 , OrdGB --주문유형
		   			 , X.OutNo   --출고번호
		   			 , X.CustNm  --거래처명	 
		   			 , X.EquipCustNm --설비업체
		   			 , X.Site        --현장
		   			 , X.DeliveryPlace --화물지점
		   			 , X.TelNo         --전화번호
		   			 , X.ItemNm        --품목명
		   			 , X.Spec          --규격
		   			 , X.OutQty        --출고수량
		   			 , X.SalesPrice    --단가
		   			 , X.SupplyAmt     --공급가액
		   			 , X.Vat           --부가세
		   			 , X.Amt           --합계
		   			 , X.ItemNo        --품목코드(H)
		   			 , X.CustCd        --거래처코드(H)
		   			 , ORD             --정렬순번(H)
					 , ORD2            --정렬순번(H)
		   			 , CASE WHEN OrdGB IN('총계') THEN 0 ELSE X.SupplyAmt END AS SUPPLYAMTSUM	--소계 포함 되지 않은 공급가액 합계
		   			 , CASE WHEN OrdGB IN('총계') THEN 0 ELSE X.Vat	     END AS VATSUM		--소계 포함 되지 않은 부가세 합계
		   			 , CASE WHEN OrdGB IN('총계') THEN 0 ELSE X.Amt       END AS AMTSUM		--소계 포함 되지 않은 합계의 합계
				   FROM(

					SELECT X.OutYmd  --출고일자
						 , X.ORDGB --ISNULL(MISTW.DBO.fn_comCodeNm('LOG005',X.OrdGB,1),'')  AS OrdGB --주문유형
						 , X.OutNo   --출고번호
						 , X.CustNm  --거래처명	 
						 , X.EquipCustNm --설비업체
						 , X.Site        --현장
						 , X.DeliveryPlace --화물지점
						 , X.TelNo         --전화번호
						 , X.ItemNm        --품목명
						 , X.Spec          --규격
						 , X.OutQty        --출고수량
						 , X.SalesPrice    --단가
						 , X.SupplyAmt     --공급가액
						 , X.Vat           --부가세
						 , X.Amt           --합계
						 , X.ItemNo        --품목코드(H)
						 , X.CustCd        --거래처코드(H)
						 , ORD             --정렬순번(H)
						 , ORD2           --정렬순번(H)
					 FROM
					(SELECT 
						  A.OutYmd  --출고일자
						 , A.ITEMGRPCD + '_제품'   AS ORDGB
						 , A.OutNo   --출고번호
						 , D.CustNm  --거래처명	 
						 , A.EquipCustNm --설비업체
						 , A.Adr AS SITE   --현장
						 , A.DeliveryPlace --화물지점
						 , A.TelNo         --전화번호
						 , E.ItemNm        --품목명
						 , E.Spec          --규격
						 , B.OutQty        --출고수량
						 , B.SalesPrice    --단가
						 , B.SupplyAmt     --공급가액
						 , B.Vat           --부가세
						 , B.Amt           --합계
						 , B.ItemNo        --품목코드
						 , A.CustCd        --거래처코드
						 , 0 AS ORD             --정렬순번(H)
						 , 0 AS ORD2            --정렬순번(H)
					  FROM MISTW..tbLogOut A
					  INNER JOIN MISTW..tbLogOutDtl B
					  ON A.OutEnrNo = B.OutEnrNo 
					  INNER JOIN MISSA..SACust D
					  ON A.CustCd  = D.CustCd 
					  INNER JOIN MISPD..PDItemMaster E
					  ON B.ITEMNO = E.ITEMNO			  
				   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				     --AND E.AcctGb <> '22'      -- 계정이 운반비 인것만
					 AND B.ItemNo NOT IN ('XS1-HM01','5XS1-HM02')
					 AND A.CustCd = @iCustCd
					--물류화물비(일별합산)
					UNION ALL
					SELECT 
						  A.OutYmd    --출고일자
						 , A.ITEMGRPCD + '_물류화물비'   --주문유형
						 , ''           --출고번호
						 , MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명 
						 , ''           --설비업체
						 , ''           --현장
						 , ''           --화물지점
						 , ''           --전화번호
						 , '물류화물비'        --품목명
						 , ''              --규격
						 , 0               --출고수량
						 , 0               --단가
						 , SUM(ISNULL(B.SupplyAmt,0)) AS SUPPLYAMT     --공급가액
						 , SUM(ISNULL(B.Vat,0))       AS VAT    --부가세
						 , SUM(ISNULL(B.Amt,0))       AS AMT    --합계
						 , ''              --품목코드
						 , A.CustCd        --거래처코드
						 , 1 AS ORD             --정렬순번(H)
						 , 0 AS ORD2            --정렬순번(H)
					  FROM MISTW..tbLogOut A
					  INNER JOIN MISTW..tbLogOutDtl B
					  ON A.OutEnrNo = B.OutEnrNo 
					  INNER JOIN MISSA..SACust D
					  ON A.CustCd  = D.CustCd 
					  INNER JOIN MISPD..PDItemMaster E
					  ON B.ITEMNO = E.ITEMNO			  
				   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				     --AND E.AcctGb ='22'      -- 계정이 운반비 인것만
					 AND B.ItemNo IN ('XS1-HM01','5XS1-HM02')
					 AND A.CustCd = @iCustCd
				   GROUP BY A.OutYmd ,A.CustCd,A.ITEMGRPCD
				   HAVING SUM(ISNULL(B.Amt,0)) > 0
					UNION ALL
					--공구 판매
					SELECT	DD.IssueYmd
						 , A.ITEMGRPCD + '_공구판매'
						 ,	DD.DeliveryNo
						 ,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
						 ,	MISTW.DBO.fn_gtcEquipNm(Y.EquipCustCd ) AS EQUIPCUSTNM
						 ,	MISTW.DBO.fn_gtcSiteNm(Y.SiteCd)         AS SITE
						 ,	MISTW.DBO.fn_gtcDpNm(Y.DeliveryPlace)  AS DeliveryPlace
						 ,	Y.TelNo
						 ,	C.ClassNM
						 ,	C.Spec
						 ,	DD.qty
						 ,	DD.Price
						 ,	DD.SupAmt
						 ,	DD.Vat
						 ,	DD.Amt
						 ,	DD.LotNo
						 ,	A.CustCd
						 , 0 AS ORD             --정렬순번(H)
						 , 0 AS ORD2            --정렬순번(H)
					  FROM MISTW..tbGtcOrdEnr A
					  JOIN MISTW..tbGtcOrdEnrDtl B ON B.OrdEnrNo = A.OrdEnrNo
					  JOIN MISTW..tbGtcSaleOut X
							ON B.OrdEnrDtlNo = X.OrdEnrDtlNo 
					  JOIN MISTO..ToDeliveryDetail DD 
							ON	DD.DeliveryNo = X.DeliveryNo 
							AND DD.Seq        = X.Seq					
					  JOIN MISTO..TODeliveryMaster Y
							ON	DD.DeliveryNo = Y.DeliveryNo 					
					  JOIN MISTO..TOClass C ON C.ClassCd = DD.ClassCD AND C.itemGrpcd = DD.ItemGrpCd
					 WHERE A.CustCd = @iCustCd
					   AND A.OrdGb IN ('SP02', 'GP02')
					   AND Y.DeliveryYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
					UNION ALL
					/*SP공구임대료*/
					SELECT  C.ThisMonDeadYmd
						, A.ITEMGRPCD +  '_임대료'
						, ''
						, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
						, A.EQUIPCUSTNM 
						, A.SITENM 
						, ''
						, ''
						, ''
						, ''
						, ''
						, ''			
						, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
						, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
						, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
						, ''
						, A.CUSTCD	
						, 0 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)
					FROM MISTO..TORentCalcHistory A
					INNER JOIN MISSA..SACust B
						ON A.CUSTCD = B.CustCd 
					INNER JOIN MISTO..TOCustDeadYmdMgm C
						ON A.CUSTCD = C.CustCd 
						AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
						AND C.CalYm     = A.STANDARDYM
						AND C.ItemGrpCd ='SP'					
					WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)												
					  AND A.CUSTCD = @iCustCd										
					GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
					HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0
					UNION ALL
					/*GP공구임대료*/
					SELECT  C.ThisMonDeadYmd
						, A.ITEMGRPCD +  '_임대료'
						, ''
						, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
						, A.EQUIPCUSTNM 
						, A.SITENM 
						, ''
						, ''
						, ''
						, ''
						, ''
						, ''			
						, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
						, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
						, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
						, ''
						, A.CUSTCD	
						, 0 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)
					FROM MISTO..TORentCalcHistoryGP A
					INNER JOIN MISSA..SACust B
						ON A.CUSTCD = B.CustCd 
					INNER JOIN MISTO..TOCustDeadYmdMgm C
						ON A.CUSTCD = C.CustCd 
						AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
						AND C.CalYm     = A.STANDARDYM
						AND C.ItemGrpCd ='GP'					
					WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)												
					  AND A.CUSTCD = @iCustCd		
					GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
					HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0   
					/*AS연체료*/
					UNION ALL
					SELECT A.CALCYMD
						, A.ITEMGRPCD +  '_AS연체료'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
						,	MISTW.DBO.fn_gtcEquipNm(A.EquipCustCd) AS EQUIPCUSTNM
						,	MISTW.DBO.fn_gtcSiteNm(A.SiteCD)         AS SITE
						,	MISTW.DBO.fn_gtcDpNm(A.DeliveryPlace)  AS DeliveryPlace
						,	''  AS TELNO 
						,	MISTW.DBO.fn_gtcToolNm('2',A.ITEMGRPCD,B.LotNo)     AS ItemNm
						,	MISTW.DBO.fn_gtcToolSpec('2',A.ITEMGRPCD,B.LotNo)   AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice 
						,   B.RealLateFee                            AS SUPPLYAMT --공급가액
						,   B.RealLateFee * 0.1                      AS VAT       --부가세
						,   B.RealLateFee + (B.RealLateFee * 0.1) AS AMT       --금액
						,	B.LotNo             AS ItemNo
						, A.CustCd       AS CUSTCD
						, 0 AS ORD             --정렬순번(H)	
						, 0 AS ORD2            --정렬순번(H)			  
					FROM MISTO..tbASLate A
					INNER JOIN MISTO..tbASLateDtl B
					   ON A.AsLateMgmNo = B.AsLateMgmNo 
					INNER JOIN MISSA..SACust C
					   ON A.CUSTCD    = C.CustCd 
					WHERE 1=1 --A.CalcYm    = LEFT(@iOutYmdTo,6)					
					  AND A.CALCYMD   BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.CustCd = @iCustCd 			  			
				   /*AS청구비*/
					UNION ALL
					SELECT A.SubmitYmd
						,  C.ITEMGRPCD +  '_AS청구비'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(C.CustCd)	AS CUSTNM
						,	MISTW.DBO.fn_gtcEquipNm(B.EquipCustCD) AS EQUIPCUSTNM
						,	MISTW.DBO.fn_gtcSiteNm(B.SiteCD)         AS SITE
						,	MISTW.DBO.fn_gtcDpNm(B.DeliveryPlace)  AS DeliveryPlace
						,	''  AS TELNO 
						,	MISTW.DBO.fn_gtcToolNm('2',C.ITEMGRPCD,C.LotNo)     AS ItemNm
						,	MISTW.DBO.fn_gtcToolSpec('2',C.ITEMGRPCD,C.LotNo)   AS Spec
						,	1                         AS OutQty
						,	SUM(ISNULL(Price,0))      AS SalesPrice
						,   SUM(E.SupAmt)             AS SUPPLYAMT --공급가액
						,   SUM(E.SupAmt) * 0.1       AS VAT       --부가세
						,   SUM(E.SupAmt) + SUM(E.SupAmt) * 0.1            AS AMT       --금액
						,	''             AS ItemNo
						, C.CustCd         AS CUSTCD
						, 0 AS ORD             --정렬순번(H)	
						, 0 AS ORD2            --정렬순번(H)			  
						FROM MISTO..TOAsFault  A
						INNER JOIN MISTO..TOAs C
						   ON A.AsNo = C.AsNo 
						INNER JOIN MISTO..TODeliveryMaster B
						   ON C.DeliveryNo           = B.DeliveryNo 
						INNER JOIN MISSA..SACust D
						   ON B.CUSTCD               = D.CustCd 
						INNER JOIN MISTO..TOAsPart E
						   ON A.AsNo = E.AsNo 
						   AND A.Seq = E.Seq 
						WHERE A.SubmitYn             = 'Y'           --청구여부가 'Y' 인 건
						  AND A.SubmitYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
						  AND C.CustCD = @iCustCd 					
						  AND E.SupAmt  > 0
						GROUP BY A.SubmitYmd,C.ITEMGRPCD ,C.CustCd,B.EquipCustCD,B.SiteCD,B.DeliveryPlace,C.LotNo
		   			/*공구분실*/
					UNION ALL
						SELECT A.LOSSYMD
							,  A.ITEMGRPCD +  '_공구분실'
							,	''  AS DELIVERYNO  --출고번호
							,	MISTW.DBO.fn_comCustNm(B.CustCd)	AS CUSTNM
							,	MISTW.DBO.fn_gtcEquipNm(B.EquipCustCD) AS EQUIPCUSTNM
							,	MISTW.DBO.fn_gtcSiteNm(B.SiteCD)         AS SITE
							,	MISTW.DBO.fn_gtcDpNm(B.DeliveryPlace)  AS DeliveryPlace
							,	''  AS TELNO 
							,	MISTW.DBO.fn_gtcToolNm('2',A.ITEMGRPCD,A.LotNo)     AS ItemNm
							,	MISTW.DBO.fn_gtcToolSpec('2',A.ITEMGRPCD,A.LotNo)   AS Spec
							,	1              AS OutQty
							,	0              AS SalesPrice
							, A.Amt                 AS SUPPLYAMT --공급가액
							, A.Amt * 0.1           AS VAT       --부가세
							, A.Amt + A.Amt * 0.1   AS AMT       --금액                    
							, A.LotNo               AS ItemNo
   			  				, C.CustCd              AS CUSTCD
							, 0 AS ORD             --정렬순번(H)				  
							, 0 AS ORD2            --정렬순번(H)
						FROM MISTO..ToLoss A
						INNER JOIN MISTO..TODeliveryMaster B
						   ON A.DeliveryNo           = B.DeliveryNo 
						INNER JOIN MISSA..SACust C
						   ON B.CUSTCD               = C.CustCd 				
						WHERE A.LossYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
						  AND A.AMT                  > 0		
						  AND B.CustCd = @iCustCd 			  										
					/*공구분실취소*/
					UNION ALL
						SELECT A.CancelYmd 
							,  A.ITEMGRPCD +  '_공구분실취소'
							,	''  AS DELIVERYNO  --출고번호
							,	MISTW.DBO.fn_comCustNm(B.CustCd)	AS CUSTNM
							,	MISTW.DBO.fn_gtcEquipNm(B.EquipCustCD) AS EQUIPCUSTNM
							,	MISTW.DBO.fn_gtcSiteNm(B.SiteCD)         AS SITE
							,	MISTW.DBO.fn_gtcDpNm(B.DeliveryPlace)  AS DeliveryPlace
							,	''  AS TELNO 
							,	MISTW.DBO.fn_gtcToolNm('2',A.ITEMGRPCD,A.LotNo)     AS ItemNm
							,	MISTW.DBO.fn_gtcToolSpec('2',A.ITEMGRPCD,A.LotNo)   AS Spec
							,	1              AS OutQty
							,	0              AS SalesPrice
							, A.Amt * -1                 AS SUPPLYAMT --공급가액
							, A.Amt * 0.1 * -1           AS VAT       --부가세
							, (A.Amt*-1) + (A.Amt * 0.1 * -1)   AS AMT       --금액                    
							, A.LotNo               AS ItemNo
   			  				, C.CustCd              AS CUSTCD
							, 0 AS ORD             --정렬순번(H)	
							, 0 AS ORD2            --정렬순번(H)			  
						FROM MISTO..ToLoss A
						INNER JOIN MISTO..TODeliveryMaster B
						   ON A.DeliveryNo           = B.DeliveryNo 
						INNER JOIN MISSA..SACust C
						   ON B.CUSTCD               = C.CustCd 
						WHERE A.CancelYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
						  AND A.AMT                  > 0		
						  AND B.CustCd = @iCustCd 
					/*기타청구*/
					UNION ALL		    
					SELECT A.DELIVERYYMD
							,  A.ITEMGRPCD +  '_기타청구'
							,	''  AS DELIVERYNO  --출고번호
							,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
							,	MISTW.DBO.fn_gtcEquipNm(A.EquipCustCD) AS EQUIPCUSTNM
							,	MISTW.DBO.fn_gtcSiteNm(A.SiteCD)         AS SITE
							,	MISTW.DBO.fn_gtcDpNm(A.DeliveryPlace)  AS DeliveryPlace
							,	''  AS TELNO 
							,	C.ItemDNm      AS ItemNm
							,	''             AS Spec
							, Qty              AS OutQty
							, C.price             AS SalesPrice
							, C.SUPPLYAMT      AS SUPPLYAMT --공급가액
							, C.VAT            AS VAT       --부가세
							, C.Amt            AS AMT       --금액
							, ''                    AS ItemNo
   			  				, A.CustCd              AS CUSTCD
							, 0 AS ORD             --정렬순번(H)
							, 0 AS ORD2            --정렬순번(H)				  										
					FROM MISTO..tbGtcTrade A
					INNER JOIN MISSA..SACust B
					  ON A.CustCd = B.CustCd 
					INNER JOIN MISTO..tbGtcTradeDetail C
					  ON C.TradeMgmNo = A.TradeMgmNo 
					WHERE A.CustCd = @iCustCd
					  AND A.DELIVERYYMD BETWEEN @w_OutYmdFr AND @iOutYmdTo			  
					/*공구화물비*/
					UNION ALL	   		    
					SELECT	MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2))           --패킹일자
						, A.ITEMGRPCD +  '_공구화물비'
						,	''  AS DELIVERYNO  --출고번호
						,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM --거래처명	 
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO      
						,	'공구화물비'       AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice 
						,	SUM(ISNULL(B.TransitFee,0))   AS SUPPLYAMT
						,	SUM(ISNULL(B.TransitVat,0))   AS VAT
						,	SUM(ISNULL(B.TransitSum,0))   AS AMT
						,	''             AS ItemNo
						,	A.CustCd       AS CUSTCD
						, 1 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)
					FROM MISTW..tbGtcOrdEnr A
					JOIN MISTW..tbGtcPkgEnr B ON B.OrdEnrNo = A.OrdEnrNo
					 AND MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					WHERE 1=1 
					AND A.CustCd = @iCustCd
					AND A.OutStat ='04'
					GROUP BY MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) , A.CustCd,A.ITEMGRPCD
					HAVING SUM(ISNULL(B.TransitSum,0)) > 0
					 ) X
				WHERE (@iOrdGb = '1' AND x.OrdGb = 'SP01')
					OR (@iOrdGb = '2' AND x.OrdGb = 'GP01')
					OR (@iOrdGb = '3' AND x.OrdGb IN ('SP02','SP03','SP04','SP05','SP06' ))
					OR (@iOrdGb = '4' AND x.OrdGb IN ('GP02','GP03','GP04','GP05','GP06' ))
					OR  (@iOrdGb = '%' AND x.OrdGb lIKE '%')
				UNION ALL --총계
				SELECT     @iOutYmdTo      AS OutYmd  --출고일자
						 , '총계'  AS OrdGB --주문유형
						 , ''      AS OutNo   --출고번호
						 , ''      AS CustNm  --거래처명	 
						 , ''      AS EquipCustNm --설비업체
						 , ''      AS Site        --현장
						 , ''      AS DeliveryPlace --화물지점
						 , ''      AS TelNo         --전화번호
						 , ''      AS ItemNm        --품목명
						 , ''      AS Spec          --규격
						 , 0       AS OutQty        --출고수량
						 , 0       AS SalesPrice    --단가
						 , SUM(X.SupplyAmt) AS SupplyAmt     --공급가액
						 , SUM(X.Vat)       AS Vat    --부가세
						 , SUM(X.Amt)       AS Amt    --합계
						 , ''      AS ItemNo        --품목코드(H)
						 , ''      AS CustCd        --거래처코드(H)
						 , ORD             --정렬순번(H)
						 , ORD2            --정렬순번(H)
					 FROM
					(SELECT 
						  A.OutYmd  --출고일자
						 , C.OrdGB --주문유형
						 , A.OutNo   --출고번호
						 , D.CustNm  --거래처명	 
						 , A.EquipCustNm --설비업체
						 , A.Adr AS SITE        --현장
						 , A.DeliveryPlace --화물지점
						 , A.TelNo         --전화번호
						 , E.ItemNm        --품목명
						 , E.Spec          --규격
						 , B.OutQty        --출고수량
						 , B.SalesPrice    --단가
						 , B.SupplyAmt     --공급가액
						 , B.Vat           --부가세
						 , B.Amt           --합계
						 , B.ItemNo        --품목코드
						 , A.CustCd        --거래처코드
						 , 3 AS ORD             --정렬순번(H)
						 , 0 AS ORD2            --정렬순번(H)
					  FROM MISTW..tbLogOut A
					  INNER JOIN MISTW..tbLogOutDtl B
					  ON A.OutEnrNo = B.OutEnrNo 
					  INNER JOIN tbLogOrdEnr C
					  ON B.OrdEnrNo = C.OrdEnrNo  
					  INNER JOIN MISSA..SACust D
					  ON A.CustCd  = D.CustCd 
					  INNER JOIN MISPD..PDItemMaster E
					  ON B.ITEMNO = E.ITEMNO			  
				   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				     --AND E.AcctGb <>'22'      -- 계정이 운반비 인것만
					 AND B.ItemNo NOT IN ('XS1-HM01','5XS1-HM02')
					 AND A.CustCd = @iCustCd
					--물류화물비(일별합산)
					UNION ALL
					SELECT 
						  A.OutYmd    --출고일자
						 , ''         --주문유형
						 , ''           --출고번호
						 , ''           --거래처명	 
						 , ''           --설비업체
						 , ''           --현장
						 , ''           --화물지점
						 , ''           --전화번호
						 , '화물비'        --품목명
						 , ''              --규격
						 , 0               --출고수량
						 , SUM(ISNULL(Price,0))                       --단가
						 , SUM(ISNULL(B.SupplyAmt,0)) AS SUPPLYAMT     --공급가액
						 , SUM(ISNULL(B.Vat,0))       AS VAT    --부가세
						 , SUM(ISNULL(B.Amt,0))       AS AMT    --합계
						 , ''              --품목코드
						 , A.CustCd        --거래처코드
						 , 3 AS ORD             --정렬순번(H)
						 , 0 AS ORD2            --정렬순번(H)
					  FROM MISTW..tbLogOut A
					  INNER JOIN MISTW..tbLogOutDtl B
					  ON A.OutEnrNo = B.OutEnrNo 
					  INNER JOIN MISSA..SACust D
					  ON A.CustCd  = D.CustCd 
					  INNER JOIN MISPD..PDItemMaster E
					  ON B.ITEMNO = E.ITEMNO			  
				   WHERE A.OutYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
					 AND B.ItemNo = E.ItemNo -- IN ('XS1-HM01','5XS1-HM02')
				     --AND E.AcctGb ='22'      -- 계정이 운반비 인것만
					 AND B.ItemNo IN ('XS1-HM01','5XS1-HM02')
					 AND A.CustCd = @iCustCd
				   GROUP BY A.OutYmd ,A.CustCd
				   HAVING SUM(ISNULL(B.Amt,0)) > 0
					UNION ALL
					--공구 판매
					SELECT	DD.IssueYmd
						 ,	A.OrdGB
						 ,	DD.DeliveryNo
						 ,	MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
						 ,	A.EquipCustNm
						 ,	A.Site
						 ,	A.DeliveryPlace
						 ,	A.TelNo
						 ,	C.ClassNM
						 ,	C.Spec
						 ,	DD.qty
						 ,	DD.Price
						 ,	DD.SupAmt
						 ,	DD.Vat
						 ,	DD.Amt
						 ,	DD.LotNo
						 ,	A.CustCd
						 , 3 AS ORD             --정렬순번(H)
						 , 0 AS ORD2            --정렬순번(H)
					  FROM MISTW..tbGtcOrdEnr A
					  JOIN MISTW..tbGtcOrdEnrDtl B ON B.OrdEnrNo = A.OrdEnrNo
					  JOIN MISTW..tbGtcSaleOut X
							ON B.OrdEnrDtlNo = X.OrdEnrDtlNo 
					  JOIN MISTO..ToDeliveryDetail DD 
							ON	DD.DeliveryNo = X.DeliveryNo 
							AND DD.Seq        = X.Seq					
					  JOIN MISTO..TOClass C ON C.ClassCd = DD.ClassCD AND C.itemGrpcd = DD.ItemGrpCd
					  JOIN MISTO..TODeliveryMaster E
					        ON DD.DeliveryNo = E.DeliveryNo  
					 WHERE A.CustCd = @iCustCd
					   AND A.OrdGb IN ('SP02', 'GP02')
					   AND E.DeliveryYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
					UNION ALL
					/*SP공구임대료*/
					SELECT  C.ThisMonDeadYmd
						, A.ItemGrpCd+'_임대료' 
						, ''
						, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
						, A.EQUIPCUSTNM 
						, A.SITENM 
						, ''
						, ''
						, ''
						, ''
						, ''
						, ''			
						, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
						, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
						, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
						, ''
						, A.CustCd
						, 3 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)
					FROM MISTO..TORentCalcHistory A
					INNER JOIN MISSA..SACust B
						ON A.CUSTCD = B.CustCd 
					INNER JOIN MISTO..TOCustDeadYmdMgm C
						ON A.CUSTCD = C.CustCd 
						AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
						AND C.CalYm     = A.STANDARDYM
						AND C.ItemGrpCd ='SP'					
					WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)												
					  AND A.CUSTCD = @iCustCd	
					GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
					HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0
					UNION ALL
					/*GP공구임대료*/
					SELECT  C.ThisMonDeadYmd
						, A.ItemGrpCd+'_임대료' 
						, ''
						, MISTW.DBO.fn_comCustNm(A.CustCd)	AS CUSTNM
						, A.EQUIPCUSTNM 
						, A.SITENM 
						, ''
						, ''
						, ''
						, ''
						, ''
						, ''			
						, SUM(CONVERT(MONEY,A.RAMT))                                           AS SUPPLYAMT     --공급가액
						, SUM(CONVERT(MONEY,A.RAMT) * 0.1)                                     AS VAT           --부가세
						, SUM(CONVERT(MONEY,A.RAMT) +  (CONVERT(MONEY,A.RAMT) * 0.1))          AS AMT			
						, ''
						, A.CustCd
						, 3 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)
					FROM MISTO..TORentCalcHistoryGP A
					INNER JOIN MISSA..SACust B
						ON A.CUSTCD = B.CustCd 
					INNER JOIN MISTO..TOCustDeadYmdMgm C
						ON A.CUSTCD = C.CustCd 
						AND C.ThisMonDeadYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo 
						AND C.CalYm     = A.STANDARDYM
						AND C.ItemGrpCd ='GP'					
					WHERE A.STANDARDYM  BETWEEN LEFT(@iOutYmdFr,6) AND LEFT(@iOutYmdTo,6)											
					  AND A.CUSTCD = @iCustCd	
					GROUP BY A.EQUIPCUSTNM,A.SITENM,C.ThisMonDeadYmd,A.CUSTCD,STANDARDYM,A.ItemGrpCd
					HAVING SUM(CONVERT(MONEY,A.RAMT)) > 0		   
					/*AS연체료*/
					UNION ALL
					SELECT A.CALCYMD
						, A.ItemGrpCd+'_AS연체료'
						,	''  AS DELIVERYNO  --출고번호
						,	''	AS CUSTNM      --거래처명	 
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	A.ItemGrpCd+'_AS연체료'     AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice 
						, SUM(B.RealLateFee)                            AS SUPPLYAMT --공급가액
						, SUM(B.RealLateFee) * 0.1                      AS VAT       --부가세
						, SUM(B.RealLateFee) + SUM(B.RealLateFee) * 0.1 AS AMT       --금액
						,	''             AS ItemNo
						, A.CustCd       AS CUSTCD
						, 3 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)				  
					FROM MISTO..tbASLate A
					INNER JOIN MISTO..tbASLateDtl B
					   ON A.AsLateMgmNo = B.AsLateMgmNo 
					INNER JOIN MISSA..SACust C
					   ON A.CUSTCD    = C.CustCd 
					WHERE 1=1 --A.CalcYm    = LEFT(@iOutYmdTo,6)					
					  AND A.CALCYMD   BETWEEN @w_OutYmdFr AND @iOutYmdTo
					  AND A.CustCd = @iCustCd 			  
					GROUP BY A.CUSTCD,A.CALCYMD,A.ItemGrpCd
					UNION ALL
					/*AS청구비*/
					SELECT A.SubmitYmd
						,  C.ITEMGRPCD +  '_AS청구비'
						,	''  AS DELIVERYNO  --출고번호
						,	''	AS CUSTNM      --거래처명	 
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO 
						,	C.ItemGrpCd+'_AS청구비'     AS ItemNm
						,	''             AS Spec
						, SUM(ISNULL(E.qty,0))              AS OutQty
						, SUM (ISNULL(Price,0))            AS SalesPrice
						, SUM(ISNULL(E.SupAmt,0))         AS SUPPLYAMT --공급가액
						, SUM(ISNULL(E.SupAmt,0)) * 0.1   AS VAT       --부가세
						, SUM(ISNULL(E.SupAmt,0)) + SUM(ISNULL(E.SupAmt,0)) * 0.1            AS AMT       --금액
						,	''             AS ItemNo
						, C.CustCd       AS CUSTCD
						, 3 AS ORD             --정렬순번(H)		
						, 0 AS ORD2            --정렬순번(H)		  
						FROM MISTO..TOAsFault  A --FROM
						INNER JOIN MISTO..TOAs C
						   ON A.AsNo = C.AsNo 
						INNER JOIN MISTO..TODeliveryMaster B
						   ON C.DeliveryNo           = B.DeliveryNo 
						INNER JOIN MISSA..SACust D
						   ON B.CUSTCD               = D.CustCd 
						INNER JOIN MISTO..TOAsPart E
						   ON A.AsNo = E.AsNo 
						   AND A.Seq = E.Seq 
						WHERE A.SubmitYn             = 'Y'           --청구여부가 'Y' 인 건
						  AND A.SubmitYmd  BETWEEN @w_OutYmdFr AND @iOutYmdTo
						  AND C.CustCd = @iCustCd 			  
						GROUP BY A.SubmitYmd,C.CUSTCD,C.ItemGrpCd
						HAVING SUM(E.SupAmt)  > 0
					/*공구분실*/
					UNION ALL
						SELECT A.LOSSYMD
							,  A.ITEMGRPCD +  '_공구분실'
							,	''  AS DELIVERYNO  --출고번호
							,	''	AS CUSTNM      --거래처명	 
							,	''  AS EQUIPCUSTNM
							,	''  AS SITE
							,	''  AS DELIVERYPLACE
							,	''  AS TELNO 
							,	A.ItemGrpCd+'_공구분실'     AS ItemNm
							,	''             AS Spec
							,	0              AS OutQty
							,	0              AS SalesPrice
							, SUM(A.Amt)                 AS SUPPLYAMT --공급가액
							, SUM(A.Amt) * 0.1           AS VAT       --부가세
							, SUM(A.Amt) + (SUM(A.Amt) * 0.1)   AS AMT       --금액                    
							, ''                    AS ItemNo
   			  				, B.CustCd              AS CUSTCD
							, 3 AS ORD             --정렬순번(H)
							, 0 AS ORD2            --정렬순번(H)				  
						FROM MISTO..ToLoss A
						INNER JOIN MISTO..TODeliveryMaster B
						   ON A.DeliveryNo           = B.DeliveryNo 
						INNER JOIN MISSA..SACust C
						   ON B.CUSTCD               = C.CustCd 
						WHERE A.LossYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
						  AND A.AMT                  > 0		
						  AND B.CustCd = @iCustCd
						GROUP BY A.LOSSYMD,B.CUSTCD,A.ItemGrpCd				   			  										
					/*공구분실취소*/
					UNION ALL
						SELECT A.CancelYmd 
							,  A.ITEMGRPCD +  '_공구분실취소'
							,	''  AS DELIVERYNO  --출고번호
							,	''	AS CUSTNM      --거래처명	 
							,	''  AS EQUIPCUSTNM
							,	''  AS SITE
							,	''  AS DELIVERYPLACE
							,	''  AS TELNO 
							,	A.ItemGrpCd+'_공구분실취소'     AS ItemNm
							,	''             AS Spec
							,	0              AS OutQty
							,	0              AS SalesPrice
							, SUM(A.Amt) * -1                AS SUPPLYAMT --공급가액
							, SUM(A.Amt) * 0.1 * -1           AS VAT       --부가세
							, (SUM(A.Amt) + (SUM(A.Amt) * 0.1))*-1   AS AMT       --금액                                    
							, ''                    AS ItemNo
   			  				, B.CustCd              AS CUSTCD
							, 3 AS ORD             --정렬순번(H)
							, 0 AS ORD2            --정렬순번(H)				  
						FROM MISTO..ToLoss A
						INNER JOIN MISTO..TODeliveryMaster B
						   ON A.DeliveryNo           = B.DeliveryNo 
						INNER JOIN MISSA..SACust C
						   ON B.CUSTCD               = C.CustCd 
						WHERE A.CancelYmd BETWEEN @w_OutYmdFr AND @iOutYmdTo
						  AND A.AMT                  > 0		
						  AND B.CustCd = @iCustCd 
						GROUP BY A.CancelYmd ,B.CUSTCD,A.ItemGrpCd	
					/*기타청구*/
					UNION ALL		    
					SELECT A.DELIVERYYMD
							,  A.ITEMGRPCD +  '_기타청구'
							,	''  AS DELIVERYNO  --출고번호
							,	''	AS CUSTNM      --거래처명	 
							,	''  AS EQUIPCUSTNM
							,	''  AS SITE
							,	''  AS DELIVERYPLACE
							,	''  AS TELNO 
							,	A.ItemGrpCd+'_기타청구'     AS ItemNm
							,	''             AS Spec
							,	0              AS OutQty
							, SUM(ISNULL(C.Price,0))                    AS SalesPrice
							, SUM(ISNULL(C.SUPPLYAMT,0))  AS SUPPLYAMT --공급가액
							, SUM(ISNULL(C.VAT,0))        AS VAT       --부가세
							, SUM(ISNULL(C.Amt,0))        AS AMT       --금액
							, ''                    AS ItemNo
   			  				, A.CustCd              AS CUSTCD
							, 3 AS ORD             --정렬순번(H)
							, 0 AS ORD2            --정렬순번(H)				  										
					FROM MISTO..tbGtcTrade A
					INNER JOIN MISSA..SACust B
					  ON A.CustCd = B.CustCd 
					INNER JOIN MISTO..tbGtcTradeDetail C
					  ON C.TradeMgmNo = A.TradeMgmNo 
					WHERE A.CustCd = @iCustCd
					AND A.DELIVERYYMD BETWEEN @w_OutYmdFr AND @iOutYmdTo
					GROUP BY A.DELIVERYYMD ,A.CUSTCD,A.ItemGrpCd
					/*공구화물비*/
					UNION ALL	   		    
					SELECT	MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) --패킹일자
						,	A.ItemGrpCd+'_공구화물비'            --주문유형
						,	''  AS DELIVERYNO  --출고번호
						,	''	AS CUSTNM      --거래처명	 
						,	''  AS EQUIPCUSTNM
						,	''  AS SITE
						,	''  AS DELIVERYPLACE
						,	''  AS TELNO      
						,	A.ItemGrpCd+'_공구화물비'       AS ItemNm
						,	''             AS Spec
						,	0              AS OutQty
						,	0              AS SalesPrice 
						,	SUM(ISNULL(B.TransitFee,0))   AS SUPPLYAMT
						,	SUM(ISNULL(B.TransitVat,0))   AS VAT
						,	SUM(ISNULL(B.TransitSum,0))   AS AMT
						,	''             AS ItemNo
						,	A.CustCd       AS CUSTCD
						, 3 AS ORD             --정렬순번(H)
						, 0 AS ORD2            --정렬순번(H)
					FROM MISTW..tbGtcOrdEnr A
					JOIN MISTW..tbGtcPkgEnr B ON B.OrdEnrNo = A.OrdEnrNo
					 AND MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) BETWEEN @w_OutYmdFr AND @iOutYmdTo 
					WHERE 1=1 
					AND A.CustCd = @iCustCd
					AND A.OutStat ='04'
					GROUP BY MISTW.DBO.fn_ordDeliveryEnd(a.OrdEnrNo,RIGHT(OrdGB,2)) , A.CustCd,A.ItemGrpCd
					HAVING SUM(ISNULL(B.TransitSum,0)) > 0
					 ) X
				WHERE (@iOrdGb = '1' AND x.OrdGb = 'SP01')
					OR (@iOrdGb = '2' AND x.OrdGb = 'GP01')
					OR (@iOrdGb = '3' AND x.OrdGb IN ('SP02','SP03','SP04','SP05','SP06' ))
					OR (@iOrdGb = '4' AND x.OrdGb IN ('GP02','GP03','GP04','GP05','GP06' ))
					OR  (@iOrdGb = '%' AND x.OrdGb lIKE '%')
				GROUP BY X.ORD,X.ORD2  

				) X
				ORDER BY X.OUTYMD,X.ORD,X.ORD2,X.OutNo ASC
		   
			 
			END	
		END
	END
*/		
	
END
```

<a id="mistw-proc-logg022-s-06"></a>
## `MISTW.dbo.proc_LOGG022_S_06`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **운영 snapshot 원문 우선**
원본 경로: `/Users/cigro/Desktop/anasa/anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/2065442432_proc_LOGG022_S_06.sql`
운영 snapshot은 저장소에 포함되지 않은 읽기 전용 스냅샷이며, 이 문서에는 해당 snapshot 파일을 그대로 보관한다.

```sql
-- Database: MISTW
-- Object: [dbo].[proc_LOGG022_S_06]
-- Type: SQL_STORED_PROCEDURE
SET ANSI_NULLS ON;
GO
SET QUOTED_IDENTIFIER ON;
GO

CREATE PROCEDURE [dbo].[proc_LOGG022_S_06]
    @iOutYear varchar(4)
AS
BEGIN
    SET NOCOUNT ON;

    DECLARE @tmp_Amt TABLE (
         MM int
        ,SPProd money
        ,SPRent money
        ,SPGoods money
        ,EtcSale money
        ,TransFee money
        ,Support money
        ,GPJoint money
        ,FPJoint money
        ,SumAmt money
    );

    -- SalesGb
    -- 01 상품, 02 제품, 03 공구임대, 04 공구판매, 05 공구연체료
    -- 06 AS청구비, 07 공구분실, 08 임대료, 09 기타청구, 10 분실취소
    -- 11 기타, 12 물류화물비, 13 공구화물비
    INSERT INTO @tmp_Amt
    SELECT
         CONVERT(int, RIGHT(a.TotalYm, 2)) AS MM
        ,SUM(CASE
            WHEN a.SalesGb = '02' AND a.ItemGrpCd = 'SP'
            THEN a.SupplyAmt ELSE 0
         END) AS SPPROD
        ,SUM(CASE
            WHEN a.SalesGb = '03' AND a.ItemGrpCd = 'SP'
            THEN a.SupplyAmt ELSE 0
         END) AS SPRENT
        ,SUM(CASE
            WHEN a.SalesGb = '01' AND a.ItemGrpCd = 'SP'
            THEN a.SupplyAmt ELSE 0
         END) AS SPGOODS
        ,SUM(CASE
            WHEN a.ItemGrpCd NOT IN ('GP', 'FP')
             AND a.SalesGb NOT IN ('01', '02', '03', '12', '13')
            THEN a.SupplyAmt ELSE 0
         END) AS ETCSALE
        ,SUM(CASE
            WHEN a.SalesGb IN ('12', '13')
            THEN a.SupplyAmt ELSE 0
         END) AS TRANSFEE
        ,0 AS SUPPORT
        ,SUM(CASE
            WHEN a.ItemGrpCd = 'GP' AND a.SalesGb NOT IN ('12', '13')
            THEN a.SupplyAmt ELSE 0
         END) AS GPJOINT
        ,SUM(CASE
            WHEN a.ItemGrpCd = 'FP' AND a.SalesGb NOT IN ('12', '13')
            THEN a.SupplyAmt ELSE 0
         END) AS FPJOINT
        ,SUM(a.SupplyAmt) AS SUMAMT
    FROM MISTW.dbo.tbLogCustTotal AS a
    WHERE a.TotalYm BETWEEN @iOutYear + '01' AND @iOutYear + '12'
      AND ISNULL(a.CloseYmd, '') <> ''
    GROUP BY CONVERT(int, RIGHT(a.TotalYm, 2));

    WITH t_Month AS (
        SELECT 1 AS MM
        UNION ALL
        SELECT MM + 1
        FROM t_Month
        WHERE MM < 12
    )
    SELECT
         CASE a.MM
            WHEN 1 THEN '1 월'
            WHEN 2 THEN '2 월'
            WHEN 3 THEN '3 월'
            WHEN 4 THEN '4 월'
            WHEN 5 THEN '5 월'
            WHEN 6 THEN '6 월'
            WHEN 7 THEN '7 월'
            WHEN 8 THEN '8 월'
            WHEN 9 THEN '9 월'
            WHEN 10 THEN '10 월'
            WHEN 11 THEN '11 월'
            WHEN 12 THEN '12 월'
         END AS TOTALYM
        ,ISNULL(SPProd, 0) AS SPPROD
        ,ISNULL(SPRent, 0) AS SPRENT
        ,ISNULL(SPGoods, 0) AS SPGOODS
        ,ISNULL(EtcSale, 0) AS ETCSALE
        ,ISNULL(TransFee, 0) AS TRANSFEE
        ,ISNULL(Support, 0) AS SUPPORT
        ,ISNULL(GPJoint, 0) AS GPJOINT
        ,ISNULL(FPJoint, 0) AS FPJOINT
        ,ISNULL(SumAmt, 0) AS SUMAMT
        ,'' AS REMARK
    FROM t_Month AS a
    LEFT JOIN @tmp_Amt AS b
        ON b.MM = a.MM
    ORDER BY a.MM;

    SELECT
         '비율' AS TOTALYM
        ,ROUND(CONVERT(float, SUM(SPProd)) / SUM(SumAmt), 4) AS SPPROD
        ,ROUND(CONVERT(float, SUM(SPRent)) / SUM(SumAmt), 4) AS SPRENT
        ,ROUND(CONVERT(float, SUM(SPGoods)) / SUM(SumAmt), 4) AS SPGOODS
        ,ROUND(CONVERT(float, SUM(EtcSale)) / SUM(SumAmt), 4) AS ETCSALE
        ,ROUND(CONVERT(float, SUM(TransFee)) / SUM(SumAmt), 4) AS TRANSFEE
        ,ROUND(CONVERT(float, SUM(Support)) / SUM(SumAmt), 4) AS SUPPORT
        ,ROUND(CONVERT(float, SUM(GPJoint)) / SUM(SumAmt), 4) AS GPJOINT
        ,ROUND(CONVERT(float, SUM(FPJoint)) / SUM(SumAmt), 4) AS FPJOINT
        ,ROUND(CONVERT(float, SUM(SumAmt)) / SUM(SumAmt), 4) AS SUMAMT
        ,'' AS REMARK
    FROM @tmp_Amt;
END;
GO
```

<a id="mistw-proc-logc001-u-01"></a>
## `MISTW.dbo.proc_LOGC001_U_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **운영 snapshot 원문 우선**
원본 경로: `/Users/cigro/Desktop/anasa/anasa-stored-procedure-reverse-engineering/snapshots/live-schema-20260824T064641Z/MISTW/modules/sql_stored_procedure/dbo/779865845_proc_LOGC001_U_01.sql`
운영 snapshot은 저장소에 포함되지 않은 읽기 전용 스냅샷이며, 이 문서에는 해당 snapshot 파일을 그대로 보관한다.

```sql
-- Database: MISTW
-- Object: [dbo].[proc_LOGC001_U_01]
-- Type: SQL_STORED_PROCEDURE
SET ANSI_NULLS ON;
GO
SET QUOTED_IDENTIFIER ON;
GO
/********************************************************************************
제    목 : 배송비 변경
프로그램 : 출고처리
등 록 일 : 2018-09-03
등 록 자 : 양장미
수정일          수정자      내용
20260813        LITMERS     ANA-351 패킹 화물비 원장/VAT/출고 파생행 정합성
20260818        LITMERS     ANA-351 legacy ERP/API cache generation fence
-----------------------------------------------------------------------
EXEC MISTW.dbo.proc_LOGC001_U_01 11111, 3000, 300, 30, '000000881', 'ERP18'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_LOGC001_U_01]
        @ipkgEnrNo          int             -- 패킹등록번호
      , @iPaletFee          decimal         -- 파레트비용
      , @iTransitFee        decimal         -- 배송비
      , @iTransitVat        decimal         -- 호환용 입력값(서버 계산값으로 대체)
      , @iPgNo              varchar(20)     -- 프로그램번호
      , @iAddEmpNo          varchar(10)     -- 등록자
AS

DECLARE @wErrYN VARCHAR(1),
        @wErrMsg VARCHAR(1000)

DECLARE @wOrdEnrNo BIGINT,
        @wDeliveryCD VARCHAR(5),
        @wOutGb VARCHAR(5),
        @wSupplyAmt MONEY,
        @wTransitFee DECIMAL(19,4),
        @wTransitVat DECIMAL(19,4),
        @wTransitSum DECIMAL(19,4),
        @wCacheLockResult INT

BEGIN
    BEGIN TRAN

    SET NOCOUNT ON;

    SET @wErrYN = 'N'
    SET @wErrMsg = '정상 처리되었습니다.'
    SET @wTransitFee = ISNULL(@iTransitFee, 0)

    IF OBJECT_ID(N'MISTW.dbo.tbAnasaCacheGeneration', N'U') IS NULL
    BEGIN
        SET @wErrYN = 'Y'
        SET @wErrMsg = '화물비 캐시 세대 테이블을 찾을 수 없습니다.'
        GOTO ENDSTEP
    END

    EXEC @wCacheLockResult = sys.sp_getapplock
         @Resource = 'ANA-332:shipment-consumer-cache',
         @LockMode = 'Exclusive',
         @LockOwner = 'Transaction',
         @LockTimeout = 30000

    IF @wCacheLockResult < 0
    BEGIN
        SET @wErrYN = 'Y'
        SET @wErrMsg = '화물비 캐시 transaction 경계를 확보하지 못했습니다.'
        GOTO ENDSTEP
    END

    IF ISNULL(@iPaletFee, 0) < 0 OR @wTransitFee < 0
    BEGIN
        SET @wErrYN = 'Y'
        SET @wErrMsg = '파레트비와 배송비는 0원 이상이어야 합니다.'
        GOTO ENDSTEP
    END

    IF NOT EXISTS (
        SELECT 1
        FROM MISTW.dbo.tbLogPkgEnr
        WHERE pkgEnrNo = @ipkgEnrNo
    )
    BEGIN
        SET @wErrYN = 'Y'
        SET @wErrMsg = '패킹 정보를 찾을 수 없습니다.'
        GOTO ENDSTEP
    END

    -- 패킹 완료 계산(proc_LOGH002_A_01)과 동일한 무료 조건을 적용한다.
    SELECT @wOrdEnrNo = MIN(OrdEnrNo)
    FROM MISTW.dbo.tbLogPkgEnrDtl
    WHERE pkgEnrNo = @ipkgEnrNo

    SELECT @wDeliveryCD = MIN(DeliveryCD)
    FROM MISTW.dbo.tbLogOrdEnr
    WHERE OrdEnrNo = @wOrdEnrNo

    SELECT @wDeliveryCD = COALESCE(DeliveryCD, @wDeliveryCD),
           @wOutGb = OutGb
    FROM MISTW.dbo.tbLogPkgEnr
    WHERE pkgEnrNo = @ipkgEnrNo

    SELECT @wSupplyAmt = SUM(ISNULL(SupplyAmt, 0))
    FROM MISTW.dbo.tbLogOrdEnrDtl
    WHERE OrdEnrNo IN (
        SELECT OrdEnrNo
        FROM MISTW.dbo.tbLogPkgEnrDtl
        WHERE pkgEnrNo = @ipkgEnrNo
    )

    -- 출고 후 수정된 배송방법/출고구분이 있으면 출고 스냅샷을 우선한다.
    SELECT TOP (1)
           @wDeliveryCD = COALESCE(DeliveryCD, @wDeliveryCD),
           @wOutGb = COALESCE(OutGb, @wOutGb)
    FROM MISTW.dbo.tbLogOut
    WHERE pkgEnrNo = @ipkgEnrNo
    ORDER BY AddDt DESC, OutEnrNo DESC

    -- 5백만원 초과, 미납출고, 착불 택배/화물은 화물비 0원이다.
    IF ISNULL(@wSupplyAmt, 0) > 5000000
       OR @wOutGb = '03'
       OR @wDeliveryCD IN ('04', '05')
    BEGIN
        SET @wTransitFee = 0
    END

    SET @wTransitVat = FLOOR(@wTransitFee * 0.1)
    SET @wTransitSum = @wTransitFee + @wTransitVat

    IF @wTransitFee <> 0
       AND EXISTS (
            SELECT 1
            FROM MISTW.dbo.tbLogOut
            WHERE pkgEnrNo = @ipkgEnrNo
              AND OutGb IN ('01', '03')
              AND (ItemGrpCd IS NULL OR ItemGrpCd NOT IN ('SP', 'GP', 'FP'))
       )
    BEGIN
        SET @wErrYN = 'Y'
        SET @wErrMsg = '지원하지 않는 품목군의 화물비 파생행을 저장할 수 없습니다.'
        GOTO ENDSTEP
    END

    -- tbLogPkgEnr가 공식 원장이다.
    UPDATE MISTW.dbo.tbLogPkgEnr
    SET    PaletFee = ISNULL(@iPaletFee, 0)
         , TransitFee = @wTransitFee
         , TransitVat = @wTransitVat
         , TransitSum = @wTransitSum
         , PgNo = @iPgNo
         , UpdEmpNo = @iAddEmpNo
         , UpdDt = GETDATE()
    WHERE  pkgEnrNo = @ipkgEnrNo

    IF @@ERROR <> 0 OR @@ROWCOUNT <= 0
    BEGIN
        SET @wErrYN = 'Y'
        SET @wErrMsg = 'tbLogPkgEnr 테이블 저장중 오류'
        GOTO ENDSTEP
    END

    -- 판매월보 등 기존 소비자를 위한 출고 화물비 행은 원장과 동기화한다.
    UPDATE D
    SET    D.SupplyAmt = @wTransitFee
         , D.Vat = @wTransitVat
         , D.Amt = @wTransitSum
         , D.PgNo = @iPgNo
         , D.UpdEmpNo = @iAddEmpNo
         , D.UpdDt = GETDATE()
    FROM MISTW.dbo.tbLogOutDtl D
    INNER JOIN MISTW.dbo.tbLogOut O
       ON O.OutEnrNo = D.OutEnrNo
      AND O.pkgEnrNo = @ipkgEnrNo
      AND O.OutGb IN ('01', '03')
    WHERE D.ItemNo = CASE O.ItemGrpCd
                         WHEN 'SP' THEN 'XS1-HM01'
                         WHEN 'GP' THEN '5XS1-HM02'
                         WHEN 'FP' THEN '8FXS1-HM03'
                     END

    IF @@ERROR <> 0
    BEGIN
        SET @wErrYN = 'Y'
        SET @wErrMsg = 'tbLogOutDtl 테이블 배송비용 변경중 오류'
        GOTO ENDSTEP
    END

    -- 최초 출고 당시 0원이어서 파생행이 없던 건도 수정 후에는 한 행만 생성한다.
    IF @wTransitFee <> 0
    BEGIN
        INSERT INTO MISTW.dbo.tbLogOutDtl (
              OutEnrDtlNo, pkgEnrDtlNo, OrdEnrNo, OrdEnrDtlNo, OutEnrNo
            , ItemNo, Hyeopga, NegoRate, OrdQty, SupplyAmt, Vat, Amt
            , OutQty, SalesPrice, PgNo, AddEmpNo, AddDt
        )
        SELECT NEXT VALUE FOR seq_LogSequence
             , 0, 0, 0, O.OutEnrNo
             , CASE O.ItemGrpCd
                   WHEN 'SP' THEN 'XS1-HM01'
                   WHEN 'GP' THEN '5XS1-HM02'
                   WHEN 'FP' THEN '8FXS1-HM03'
               END
             , 0, 0, 0, @wTransitFee, @wTransitVat, @wTransitSum
             , 0, 0, @iPgNo, @iAddEmpNo, GETDATE()
        FROM MISTW.dbo.tbLogOut O
        WHERE O.pkgEnrNo = @ipkgEnrNo
          AND O.OutGb IN ('01', '03')
          AND O.ItemGrpCd IN ('SP', 'GP', 'FP')
          AND NOT EXISTS (
                SELECT 1
                FROM MISTW.dbo.tbLogOutDtl D
                WHERE D.OutEnrNo = O.OutEnrNo
                  AND D.ItemNo = CASE O.ItemGrpCd
                                     WHEN 'SP' THEN 'XS1-HM01'
                                     WHEN 'GP' THEN '5XS1-HM02'
                                     WHEN 'FP' THEN '8FXS1-HM03'
                                 END
          )

        IF @@ERROR <> 0
        BEGIN
            SET @wErrYN = 'Y'
            SET @wErrMsg = 'tbLogOutDtl 테이블 배송비용 저장중 오류'
            GOTO ENDSTEP
        END
    END

    UPDATE MISTW.dbo.tbAnasaCacheGeneration WITH (UPDLOCK, HOLDLOCK)
    SET    GenerationValue = GenerationValue + 1,
           UpdatedAt = SYSUTCDATETIME(),
           UpdatedBy = @iAddEmpNo,
           PgNo = @iPgNo
    WHERE  DomainName = 'shipment-freight'

    IF @@ERROR <> 0 OR @@ROWCOUNT <> 1
    BEGIN
        SET @wErrYN = 'Y'
        SET @wErrMsg = '화물비 캐시 세대 갱신중 오류'
        GOTO ENDSTEP
    END

/****************/
ENDSTEP:
/****************/
    IF @wErrYN = 'Y'
        ROLLBACK TRAN
    ELSE
        COMMIT TRAN

    SELECT @wErrYN AS ERRYN,
           @wErrMsg AS ERRMSG,
           @wTransitFee AS TRANSITFEE,
           @wTransitVat AS TRANSITVAT,
           @wTransitSum AS TRANSITSUM
END
GO
```

<a id="mistw-proc-wloc004-p-03"></a>
## `MISTW.dbo.proc_WLOC004_P_03`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_WLOC004_P_03.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_WLOC004_P_03.sql

```sql

/********************************************************************************
제    목 : 거래명세서 출력
프로그램 : 출고상태확인
등 록 일 : 2018-04-12
등 록 자 : 서도종
수정일	수정자		내용
20210712  이해권   플랙시블 품목 명세서 주소, 전화, 펙스 수정
-----------------------------------------------------------------------

EXEC proc_WLOC004_P_03 
     @iKind       ='1'     --1:거래명세서(금액포함),2:화물비명세서,3:거래명세서(금액미포함)
	,@iOrdGb      ='SP01'  --주문구분(SP01, GP01, FP01)    
	,@iItemGrpCd  ='SP'    --품목군(SP,GP,FP)      
	,@iOrdEnrNo   =NULL --주문등록번호
	,@iOutEnrNo   =38107315      --출고등록번호
	EXEC proc_WLOC004_P_03 @iKind = '2', @iOrdGb = 'SP02', @iItemGrpCd = '', @iOrdEnrNo = '4629440', @iOutEnrNo = ''
	EXEC proc_WLOC004_P_02 @iKind='2' ,@iOrdGb ='SP01' ,@iItemGrpCd  ='SP', @iOrdEnrNo   =NULL, @iOutEnrNo   = 29686617 
	EXEC proc_WLOC004_P_03 @iKind = '2', @iOrdGb = 'SP02', @iItemGrpCd = '', @iOrdEnrNo = '3264129', @iOutEnrNo = ''
	
	EXEC proc_WLOC004_P_03 @iKind = '2', @iOrdGb = 'SP02', @iItemGrpCd = '', @iOrdEnrNo = '4628691', @iOutEnrNo = ''
	EXEC proc_WLOC004_P_03 @iKind = '2', @iOrdGb = 'SP03', @iItemGrpCd = 'SP', @iOrdEnrNo = '4629366', @iOutEnrNo = ''

*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_WLOC004_P_03]	 
	 @iKind          VARCHAR(1)         --2:화물비명세서(금액 유) 4: 화물비명세서(금액 무)
	,@iOrdGb         VARCHAR(5)         --주문구분
	,@iItemGrpCd	 VARCHAR(5)
    ,@iOrdEnrNo      BIGINT     		--주문등록번호
	,@iOutEnrNo      BIGINT             --출고등록번호(미사용)

	 	 
AS	
DECLARE @w_ErrYN		VARCHAR(1)
	  , @w_ErrMsg		VARCHAR(1000)
	  , @w_CustCd       VARCHAR(5)
	  , @w_DeliveryNo   VARCHAR(15)
	  
BEGIN
	SET NOCOUNT ON;
		-- 초기화
	SET @w_ErrYN = 'N'
	SET @w_ErrMsg = '정상 처리되었습니다.'	 
	
	BEGIN
		/*주문번호에 의한 주문정보*/

		IF @iOrdGb NOT IN ('SP01','GP01','FP01') --제품
			BEGIN
				SELECT @w_CustCd = CustCd 
					FROM tbGtcOrdEnr 
					WHERE OrdEnrNo =@iOrdEnrNo 
			END
		
		IF @iOrdGb IN ('SP03','GP03','FP03') --임대
			BEGIN
				SELECT @w_DeliveryNo = A.DeliveryNo
			   	  FROM MISTW..tbGtcRentSet A
			     WHERE A.OrdEnrNo =@iOrdEnrNo 
				   AND A.RentSetNo = (SELECT MAX(RentSetNo)
				                       FROM MISTW..tbGtcRentSet
									   WHERE A.OrdEnrNo = OrdEnrNo 
									     AND ISNULL(OrdItemCanCelYn,'') <>'Y'
				                    )
			END
		ELSE IF @iOrdGb IN ('SP04','GP04','FP04') --AS
			BEGIN
				SELECT @w_DeliveryNo = A.DeliveryNo
			   	  FROM MISTW..tbGtcAsApl A
			     WHERE A.OrdEnrNo =@iOrdEnrNo 
				   AND A.AsAplMgmNo = (SELECT MAX(AsAplMgmNo)
				                       FROM MISTW..tbGtcAsApl
									   WHERE A.OrdEnrNo = OrdEnrNo 
				                    )
			END
		ELSE IF @iOrdGb IN ('SP02','GP02','FP02') --판매
			BEGIN
				SELECT @w_DeliveryNo = A.DeliveryNo
			   	  FROM MISTW..tbGtcSaleOut A
			     WHERE A.OrdEnrNo =@iOrdEnrNo 
				   AND A.GtcSalesMgmNo = (SELECT MAX(GtcSalesMgmNo)
				                       FROM MISTW..tbGtcSaleOut
									   WHERE A.OrdEnrNo = OrdEnrNo 
				                    )
			END



			/*공급받는자*/
			BEGIN
				SELECT SUBSTRING(REPLACE(BZNO,'-',''),1,3)+'-'+ SUBSTRING(REPLACE(BZNO,'-',''),4,2)+'-'+SUBSTRING(REPLACE(BZNO,'-',''),6,5)  AS REGNO  --등록번호 
						,CustNm                       AS COMNM  --상호
						,BossNm                       AS PRENM  --성명
						,Address1+''+Address2         AS ADDR   --주소
						,TelNo                        AS TELNO --전화번호
						,FaxNo                        AS FAXNO --팩스번호
						,BzSec                        AS BUSYKIND   --업태
						,BzKind                       AS BUSYTYPE   --종목       
					FROM MISSA..SACust 
					WHERE CustCd = @w_CustCd
			END

			/*공급자*/
			BEGIN
				SELECT CDDES4 AS REGNO  --등록번호
						,CDDES2 AS COMNM  --상호
						,CDDES3 AS PRENM  --성명
						,CASE WHEN SUBSTRING(@iOrdGb,1,2) = 'FP' THEN CDESTNM ELSE CDDES1 END AS ADDR   --주소
						,CASE WHEN SUBSTRING(@iOrdGb,1,2) = 'FP' THEN '031-8011-9660' ELSE '031)633-1117' END AS TELNO --전화번호
						,CASE WHEN SUBSTRING(@iOrdGb,1,2) = 'FP' THEN '070-4672-4635' ELSE '031)633-1117' END AS FAXNO --팩스번호
						,'제조업,서비스,부동산업' AS BUSYKIND   --업태
						,'배관자재,장비임대업'    AS BUSYTYPE   --종목       
					FROM MISCM..CMETCCD 
					WHERE COMKDCD ='TMB001'
			END
				/*납품정보*/
			BEGIN 
				SELECT MISTW.DBO.fn_gtcEquipNm(A.EquipCustCd) +'/'+ MISTW.DBO.fn_gtcSiteNm(A.SiteCd)  +'/'+ MISTW.DBO.fn_gtcDPNm(A.DeliveryPlace)   AS DELEVERY --납품처(설비업체+현장+화물지점)
						,A.TelNo         AS TELNO     --전화번호
						,''              AS FAXNO     -- D.MobileNo      AS FAXNO     --전화번호2
						,MISTW.DBO.fn_gtcDPNm(A.DeliveryPlace)       AS ADDR      --주소
						,''              AS CONSIGNEE --담당자  A.Consignee 
						,A.TelNo         AS MOBILENO  --CP번호
						,A.DeliveryNo 	 AS OUTNO		--출고번호
						,MISCM.dbo.fn_gfnDateTypeTrans(A.DeliveryYmd, '-')	 AS OUTYMD		--출고일자
					FROM MISTO..TODeliveryMaster  A
				WHERE A.DeliveryNo = @w_DeliveryNo
			END
				/*품목정보*/
			
			IF @iKind IN ('2', '4') --화물비명세서 경우
				BEGIN
					/*박스별 수량*/
					SELECT 'L_Box'     AS ITEMNM --품목명
					      ,''          AS SPEC   --규격 
						  ,'EA'        AS UNIT   --단위 
					      ,ISNULL(A.L_BoxQty,0)  AS OUTQTY    --수량
						  --,0	AS SALESPRICE
						  ,CASE WHEN @iKind = 2 THEN A.L_BoxFee	ELSE 0 END AS AMT
						  --,0	AS RESTQTY
					  FROM tbGtcPkgEnr A
					  INNER JOIN MISTW..tbGtcOrdEnr B
					    ON A.OrdEnrNo  = B.OrdEnrNo
						AND B.OrdEnrNo = @iOrdEnrNo
					   WHERE A.L_BoxFee > 0
					UNION ALL
					SELECT 'M_Box'     AS ITEMNM --품목명
					      ,''          AS SPEC   --규격 
						  ,'EA'          AS UNIT   --단위 
					      ,ISNULL(A.M_BoxQty,0)  AS OUTQTY    --수량
						  --,0	AS SALESPRICE
						  ,CASE WHEN @iKind = 2 THEN A.M_BoxFee	ELSE 0 END AS AMT
						  --,0	AS RESTQTY
					  FROM tbGtcPkgEnr A
					  INNER JOIN MISTW..tbGtcOrdEnr B
					    ON A.OrdEnrNo  = B.OrdEnrNo
						AND B.OrdEnrNo = @iOrdEnrNo
					   WHERE A.M_BoxFee > 0
					UNION ALL
					SELECT 'S_Box'     AS ITEMNM --품목명
					      ,''          AS SPEC   --규격 
						  ,'EA'          AS UNIT   --단위 
					      ,ISNULL(A.S_BoxQty,0)  AS OUTQTY    --수량
						  --,0	AS SALESPRICE
						  ,CASE WHEN @iKind = 2 THEN A.S_BoxFee	ELSE 0 END AS AMT
						  --,0	AS RESTQTY
					  FROM tbGtcPkgEnr A
					  INNER JOIN MISTW..tbGtcOrdEnr B
					    ON A.OrdEnrNo  = B.OrdEnrNo
						AND B.OrdEnrNo = @iOrdEnrNo
					   WHERE A.S_BoxFee > 0
					UNION ALL
					/*통별 수량*/
					SELECT 'L_Bsk'     AS ITEMNM --품목명
					      ,''          AS SPEC   --규격 
						  ,'EA'        AS UNIT   --단위 
					      ,ISNULL(A.L_BskQty,0)  AS OUTQTY    --수량
						  --,0	AS SALESPRICE
						  ,CASE WHEN @iKind = 2 THEN A.L_BskFee	ELSE 0 END AS AMT
						  --,0	AS RESTQTY
					  FROM tbGtcPkgEnr A
					  INNER JOIN MISTW..tbGtcOrdEnr B
					    ON A.OrdEnrNo  = B.OrdEnrNo
						AND B.OrdEnrNo = @iOrdEnrNo
					   WHERE A.L_BskFee > 0
					UNION ALL
					SELECT 'M_Bsk'     AS ITEMNM --품목명
					      ,''          AS SPEC   --규격 
						  ,'EA'          AS UNIT   --단위 
					      ,ISNULL(A.M_BskQty,0)  AS OUTQTY    --수량
						  --,0	AS SALESPRICE
						  ,CASE WHEN @iKind = 2 THEN A.M_BskFee	ELSE 0 END AS AMT
						  --,0	AS RESTQTY
					  FROM tbGtcPkgEnr A
					  INNER JOIN MISTW..tbGtcOrdEnr B
					    ON A.OrdEnrNo  = B.OrdEnrNo
						AND B.OrdEnrNo = @iOrdEnrNo
					   WHERE A.M_BskFee > 0
					UNION ALL
					SELECT 'S_Bsk'     AS ITEMNM --품목명
					      ,''          AS SPEC   --규격 
						  ,'EA'          AS UNIT   --단위 
					      ,ISNULL(A.S_BskQty,0)  AS OUTQTY    --수량
						  --,0	AS SALESPRICE
						  ,CASE WHEN @iKind = 2 THEN A.S_BskFee	ELSE 0 END AS AMT
						  --,0	AS RESTQTY
					  FROM tbGtcPkgEnr A
					  INNER JOIN MISTW..tbGtcOrdEnr B
					    ON A.OrdEnrNo  = B.OrdEnrNo
						AND B.OrdEnrNo = @iOrdEnrNo
					   WHERE A.S_BskFee > 0
					UNION ALL
					SELECT '파레트'    AS ITEMNM --품목명
					      ,''          AS SPEC   --규격 
						  ,''          AS UNIT   --단위 
					      ,0           AS OUTQTY    --수량
						  --,0	AS SALESPRICE
						  ,CASE WHEN @iKind = 2 THEN A.PaletFee	ELSE 0 END AS AMT
						  --,0	AS RESTQTY
					  FROM tbGtcPkgEnr A
					  INNER JOIN MISTW..tbGtcOrdEnr B
					    ON A.OrdEnrNo  = B.OrdEnrNo
						AND B.OrdEnrNo = @iOrdEnrNo
					   WHERE A.PaletFee > 0						

				/*박스총계에 대한  화물비*/
					SELECT	CASE WHEN @iKind = '2' THEN ISNULL(TransitFee,0) ELSE 0 END	AS SUPPLYAMT --공급가액   	  --택배료
						,	CASE WHEN @iKind = '2' THEN ISNULL(TransitVat,0) ELSE 0 END AS VAT       --부가세
						,	CASE WHEN @iKind = '2' THEN ISNULL(TransitSum,0) ELSE 0 END	AS AMT       --합계 		
					  FROM tbGtcPkgEnr A
					  INNER JOIN MISTW..tbGtcOrdEnr B
					    ON A.OrdEnrNo  = B.OrdEnrNo
						AND B.OrdEnrNo = @iOrdEnrNo
				END 
	END

	
END
```

<a id="mistw-proc-wloc004-p-04"></a>
## `MISTW.dbo.proc_WLOC004_P_04`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_WLOC004_P_04.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_WLOC004_P_04.sql

```sql


/********************************************************************************
제    목 : 거래명세서 출력
프로그램 : 출고상태확인
등 록 일 : 2018-04-12
등 록 자 : 서도종
수정일	수정자		내용
20210712  이해권   플랙시블 품목 명세서 주소, 전화, 펙스 수정
-----------------------------------------------------------------------

EXEC proc_WLOC004_P_04 
     @iKind       ='03'     --03:임대
	,@iCalYmFr    ='201808' --발생년월
	,@iDeliveryNo = ''
	,@iCustCd     ='AA001'
	,@iItemGrpCd  ='SP'    --품목군(SP,GP)      
    ,@iInYmdFr    ='' --입고일자
	,@iInYmdTo    ='' --입고일자

EXEC proc_WLOC004_P_04 
     @iKind       ='02'     --02:판매
	,@iCalYmFr    =''       --발생년월
	,@iDeliveryNo = 'TD20181000004'
	,@iCustCd     ='AA001'
	,@iItemGrpCd  ='SP'    --품목군(SP,GP)       	
    ,@iInYmdFr    ='' --입고일자
	,@iInYmdTo    ='' --입고일자

EXEC proc_WLOC004_P_04 
     @iKind       ='05'     --05:공구연체료
	,@iCalYmFr    ='201810' --발생년월
	,@iDeliveryNo = ''
	,@iCustCd     ='AA001'
	,@iItemGrpCd  ='SP'    --품목군(SP,GP) 
    ,@iInYmdFr    ='' --입고일자
	,@iInYmdTo    ='' --입고일자

EXEC proc_WLOC004_P_04 
     @iKind       ='06'     --06:AS청구비
	,@iCalYmFr    =''       --발생년월
	,@iDeliveryNo = '20180615034'
	,@iCustCd     ='AA001'
	,@iItemGrpCd  ='SP'    --품목군(SP,GP) 
    ,@iInYmdFr    ='20181001' --입고일자
	,@iInYmdTo    ='20181030' --입고일자

EXEC proc_WLOC004_P_04 
     @iKind       ='07'     --07:분실청구
	,@iCalYmFr    ='' --발생년월
	,@iDeliveryNo = '20180308006' 
	,@iCustCd     ='AB102'
	,@iItemGrpCd  ='SP'    --품목군(SP,GP) 
    ,@iInYmdFr    ='20181106' --분실일자
	,@iInYmdTo    ='20181106' --분실일자

EXEC proc_WLOC004_P_04 
     @iKind       ='08'     --07:분실청구
	,@iCalYmFr    ='' --발생년월
	,@iDeliveryNo = '20170201004' 
	,@iCustCd     ='AB102'
	,@iItemGrpCd  ='SP'    --품목군(SP,GP) 
    ,@iInYmdFr    ='20180910' --분실일자
	,@iInYmdTo    ='20180910' --분실일자

	EXEC proc_WLOC004_P_04 @iKind = '03', @iCalYmFr = '201811', @iDeliveryNo = '%27%27', @iCustCd = 'AA001', @iItemGrpCd = 'SP', @iInYmdFr = '', @iInYmdTo = ''
	EXEC proc_WLOC004_P_04 @iKind = '02', @iCalYmFr = '''', @iDeliveryNo = '20180608030', @iCustCd = 'AA007', @iItemGrpCd = 'SP', @iInYmdFr = '''', @iInYmdTo = ''''	
	EXEC proc_WLOC004_P_04 @iKind = '03', @iCalYmFr = '201809', @iDeliveryNo = '', @iCustCd = 'AA007', @iItemGrpCd = 'SP', @iInYmdFr = '', @iInYmdTo = ''
	EXEC proc_WLOC004_P_04 @iKind = '03', @iCalYmFr = '201810', @iDeliveryNo = '''', @iCustCd = 'AA007', @iItemGrpCd = 'SP', @iInYmdFr = '''', @iInYmdTo = ''''
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_WLOC004_P_04]	 
	 @iKind          VARCHAR(2)         --03:임대료,02:판매,05:공구연체료,06:AS청구비,07:분실청구,,08:분실청구
	,@iCalYmFr       VARCHAR(6)         --발생년월-임대료/공구연체료인 경우
	,@iDeliveryNo    VARCHAR(15)        --출고번호-판매인경우
    ,@iCustCd        VARCHAR(10)        --대리점코드
	,@iItemGrpCd     VARCHAR(2)         --품목군
	,@iInYmdFr       VARCHAR(8)         --입고일자, AS 청구비인 경우
	,@iInYmdTo       VARCHAR(8)         --입고일자, AS 청구비인 경우
	,@iSEQ           VARCHAR(2)=''      -- AS 시 껀껀히 청구를 받기위해 넘겨주는 값

	 	 
AS	
DECLARE @w_ErrYN		VARCHAR(1)
	  , @w_ErrMsg		VARCHAR(1000)
	  , @w_CustCd       VARCHAR(5)
	  
BEGIN
	SET NOCOUNT ON;
		-- 초기화
	SET @w_ErrYN = 'N'
	SET @w_ErrMsg = '정상 처리되었습니다.'	 

	BEGIN
		/*주문번호에 의한 주문정보*/
		
		SET @w_CustCd = @iCustCd		
		/*공급받는자*/
		BEGIN
			SELECT SUBSTRING(REPLACE(BZNO,'-',''),1,3)+'-'+ SUBSTRING(REPLACE(BZNO,'-',''),4,2)+'-'+SUBSTRING(REPLACE(BZNO,'-',''),6,5)   AS REGNO  --등록번호 
					,CustNm                       AS COMNM  --상호
					,BossNm                       AS PRENM  --성명
					,Address1+''+Address2         AS ADDR   --주소
					,TelNo                        AS TELNO --전화번호
					,FaxNo                        AS FAXNO --팩스번호
					,BzSec                        AS BUSYKIND   --업태
					,BzKind                       AS BUSYTYPE   --종목    
					,CASE WHEN @iKind ='03' THEN '임대료 명세서'
					      WHEN @iKind ='04' THEN '공구판매 명세서'  ---03:임대료,02:판매,05:공구연체료,06:AS청구비,07:분실청구
						  WHEN @iKind ='05' THEN '공구연체료 명세서' 
						  WHEN @iKind ='06' THEN '공구AS 청구비 명세서' 
						  WHEN @iKind ='07' THEN '공구분실청구 명세서' 	
						  WHEN @iKind ='08' THEN '공구분실취소 명세서' 							  
						  END AS RMK					   
				FROM MISSA..SACust 
				WHERE CustCd = @w_CustCd
		END

		/*공급자*/
		BEGIN
			SELECT CDDES4 AS REGNO  --등록번호
					,CDDES2 AS COMNM  --상호
					,CDDES3 AS PRENM  --성명
					,CASE WHEN @iItemGrpCd ='FP' THEN CDESTNM ELSE CDDES1 END AS ADDR   --주소
					,CASE WHEN @iItemGrpCd ='FP' THEN '031-8011-9660' ELSE '031-633-1117' END AS TELNO --전화번호
					,CASE WHEN @iItemGrpCd ='FP' THEN '070-4672-4635' ELSE '031-633-1117' END AS FAXNO --팩스번호
					,'제조업,서비스,부동산업' AS BUSYKIND   --업태
					,'배관자재,장비임대업'    AS BUSYTYPE   --종목       
				FROM MISCM..CMETCCD 
				WHERE COMKDCD ='TMB001'
		END
		
				
		IF @iKind IN ('03') --임대료
		    	/*납품정보*/
			BEGIN
				SELECT ''  AS DELEVERY --납품처(설비업체+현장+화물지점)
						,B.TelNo                     AS TELNO     --전화번호
						,''                          AS FAXNO     --전화번호2
						,B.Address1+' '+B.Address2   AS ADDR      --주소
						,''          AS CONSIGNEE --담당자  A.Consignee 
						,''          AS MOBILENO  --CP번호
						,''	         AS OUTNO		--출고번호
						,''	         AS OUTYMD		--출고일자
					FROM MISSA..SACust B
					WHERE CustCd = @w_CustCd

			IF 	@iItemGrpCd ='SP'		
				BEGIN
					/*품목리스트*/
					SELECT  ISNULL(X.ITEMNO,'')       AS ITEMNO     --품목코드
						   ,ISNULL(X.ITEMNM,'')       AS ITEMNM     --품목명
						   ,ISNULL(X.SPEC,'')         AS SPEC       --규격
						   ,ISNULL(X.UNIT,'')         AS UNIT       --단위
						   ,ISNULL(X.OUTQTY,'')       AS OUTQTY     --수량
						   ,ISNULL(X.SALESPRICE,0)    AS SALESPRICE --단가
						   ,ISNULL(X.AMT,0)           AS AMT            --임대료
						   ,'' AS REMARK
					  FROM	   
						(SELECT ''                            AS ITEMNO     --품목코드
							   ,'임대료'                     AS ITEMNM     --품목명
							   ,''                           AS SPEC       --규격
							   ,''                           AS UNIT       --단위
							   ,0                           AS OUTQTY     --수량
							   ,0                           AS SALESPRICE --단가  
							   , SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))    AS AMT            --임대료
							   ,1                            AS SEQ   
						  FROM MISTO..TORentCalcHistory A
						 WHERE A.STANDARDYM  = @iCalYmFr
						   AND A.CUSTCD      = @w_CustCd
						   AND A.ItemGrpCd   = @iItemGrpCd
						UNION ALL 
						SELECT ''                               AS ITEMNO     --품목코드
							   ,'지원금'                        AS ITEMNM     --품목명
							   ,''                              AS SPEC       --규격
							   ,''                              AS UNIT       --단위
							   ,0                              AS OUTQTY     --수량
							   ,0                              AS SALESPRICE --단가     
							   ,SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1  AS AMT        --지원금
							   ,2                            AS SEQ
						  FROM MISTO..TORentCalcHistory A
						 WHERE A.STANDARDYM  = @iCalYmFr
						   AND A.CUSTCD      = @w_CustCd
						   AND A.ItemGrpCd   = @iItemGrpCd
						   AND CONVERT(MONEY,ISNULL(A.SupportAmt,'0')) <> 0
						) X
						WHERE ISNULL(X.AMT,0)  <> 0

					/*합계*/
					SELECT SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)            AS SUPPLYAMT --공급가액
						   ,ROUND((SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)) * 0.1,0)  AS VAT       --부가세
						   ,(SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)
							) + 
							ROUND((SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)) * 0.1,0)   AS AMT        --금액
					  FROM MISTO..TORentCalcHistory A
					 WHERE A.STANDARDYM  = @iCalYmFr
					   AND A.CUSTCD      = @w_CustCd
					   AND A.ItemGrpCd   = @iItemGrpCd
				END
			ELSE IF 	@iItemGrpCd ='GP'		
				BEGIN
					/*품목리스트*/
					SELECT  ISNULL(X.ITEMNO,'')       AS ITEMNO     --품목코드
						   ,ISNULL(X.ITEMNM,'')       AS ITEMNM     --품목명
						   ,ISNULL(X.SPEC,'')         AS SPEC       --규격
						   ,ISNULL(X.UNIT,'')         AS UNIT       --단위
						   ,ISNULL(X.OUTQTY,'')       AS OUTQTY     --수량
						   ,ISNULL(X.SALESPRICE,0)    AS SALESPRICE --단가
						   ,ISNULL(X.AMT,0)           AS AMT            --임대료
						   ,ISNULL(X.LOTNO,'')        AS LOTNO
						   ,'' AS REMARK
					  FROM	   
						(SELECT ''                            AS ITEMNO     --품목코드
							   ,'임대료'                     AS ITEMNM     --품목명
							   ,''                           AS SPEC       --규격
							   ,''                           AS UNIT       --단위
							   ,0                           AS OUTQTY     --수량
							   ,0                           AS SALESPRICE --단가  
							   ,''                             AS LOTNO
							   , SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))    AS AMT            --임대료
							   ,1                            AS SEQ   
						  FROM MISTO..TORentCalcHistoryGP A
						 WHERE A.STANDARDYM  = @iCalYmFr
						   AND A.CUSTCD      = @w_CustCd
						   AND A.ItemGrpCd   = @iItemGrpCd
						UNION ALL 
						SELECT ''                               AS ITEMNO     --품목코드
							   ,'지원금'                        AS ITEMNM     --품목명
							   ,''                              AS SPEC       --규격
							   ,''                              AS UNIT       --단위
							   ,0                              AS OUTQTY     --수량
							   ,0                              AS SALESPRICE --단가     
							   ,''                             AS LOTNO
							   ,SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1  AS AMT        --지원금
							   ,2                            AS SEQ
						  FROM MISTO..TORentCalcHistoryGP A
						 WHERE A.STANDARDYM  = @iCalYmFr
						   AND A.CUSTCD      = @w_CustCd
						   AND A.ItemGrpCd   = @iItemGrpCd
						   AND CONVERT(MONEY,ISNULL(A.SupportAmt,'0')) <> 0
						) X
						WHERE ISNULL(X.AMT,0)  <> 0

					/*합계*/
					SELECT SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)            AS SUPPLYAMT --공급가액
						   ,ROUND((SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)) * 0.1,0)  AS VAT       --부가세
						   ,(SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)
							) + 
							ROUND((SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)) * 0.1,0)   AS AMT        --금액
					  FROM MISTO..TORentCalcHistoryGP A
					 WHERE A.STANDARDYM  = @iCalYmFr
					   AND A.CUSTCD      = @w_CustCd
					   AND A.ItemGrpCd   = @iItemGrpCd
				END
			ELSE IF 	@iItemGrpCd ='FP'		
				BEGIN
					/*품목리스트*/
					SELECT  ISNULL(X.ITEMNO,'')       AS ITEMNO     --품목코드
						   ,ISNULL(X.ITEMNM,'')       AS ITEMNM     --품목명
						   ,ISNULL(X.SPEC,'')         AS SPEC       --규격
						   ,ISNULL(X.UNIT,'')         AS UNIT       --단위
						   ,ISNULL(X.OUTQTY,'')       AS OUTQTY     --수량
						   ,ISNULL(X.SALESPRICE,0)    AS SALESPRICE --단가
						   ,ISNULL(X.AMT,0)           AS AMT            --임대료
						   ,ISNULL(X.LOTNO,'')        AS LOTNO
						   ,'' AS REMARK
					  FROM	   
						(SELECT ''                            AS ITEMNO     --품목코드
							   ,'임대료'                     AS ITEMNM     --품목명
							   ,''                           AS SPEC       --규격
							   ,''                           AS UNIT       --단위
							   ,0                           AS OUTQTY     --수량
							   ,0                           AS SALESPRICE --단가  
							   ,''                             AS LOTNO
							   , SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))    AS AMT            --임대료
							   ,1                            AS SEQ   
						  FROM MISTO..TORentCalcHistoryGP A
						 WHERE A.STANDARDYM  = @iCalYmFr
						   AND A.CUSTCD      = @w_CustCd
						   AND A.ItemGrpCd   = @iItemGrpCd
						UNION ALL 
						SELECT ''                               AS ITEMNO     --품목코드
							   ,'지원금'                        AS ITEMNM     --품목명
							   ,''                              AS SPEC       --규격
							   ,''                              AS UNIT       --단위
							   ,0                              AS OUTQTY     --수량
							   ,0                              AS SALESPRICE --단가     
							   ,''                             AS LOTNO
							   ,SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1  AS AMT        --지원금
							   ,2                            AS SEQ
						  FROM MISTO..TORentCalcHistoryFP A
						 WHERE A.STANDARDYM  = @iCalYmFr
						   AND A.CUSTCD      = @w_CustCd
						   AND A.ItemGrpCd   = @iItemGrpCd
						   AND CONVERT(MONEY,ISNULL(A.SupportAmt,'0')) <> 0
						) X
						WHERE ISNULL(X.AMT,0)  <> 0

					/*합계*/
					SELECT SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)            AS SUPPLYAMT --공급가액
						   ,ROUND((SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)) * 0.1,0)  AS VAT       --부가세
						   ,(SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)
							) + 
							ROUND((SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
							+ (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)) * 0.1,0)   AS AMT        --금액
					  FROM MISTO..TORentCalcHistoryFP A
					 WHERE A.STANDARDYM  = @iCalYmFr
					   AND A.CUSTCD      = @w_CustCd
					   AND A.ItemGrpCd   = @iItemGrpCd
				END


			    /*품목리스트*/
				SELECT  ISNULL(X.ITEMNO,'')       AS ITEMNO     --품목코드
				       ,ISNULL(X.ITEMNM,'')       AS ITEMNM     --품목명
					   ,ISNULL(X.SPEC,'')         AS SPEC       --규격
					   ,ISNULL(X.UNIT,'')         AS UNIT       --단위
					   ,ISNULL(X.OUTQTY,'')       AS OUTQTY     --수량
					   ,ISNULL(X.SALESPRICE,0)    AS SALESPRICE --단가
					   ,ISNULL(X.AMT,0)           AS AMT            --임대료
					   ,'' AS REMARK
				  FROM	   
					(SELECT ''                            AS ITEMNO     --품목코드
						   ,'임대료'                     AS ITEMNM     --품목명
						   ,''                           AS SPEC       --규격
						   ,''                           AS UNIT       --단위
						   ,0                           AS OUTQTY     --수량
						   ,0                           AS SALESPRICE --단가  
						   , SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))    AS AMT            --임대료
						   ,1                            AS SEQ   
					  FROM MISTO..TORentCalcHistory A
					 WHERE A.STANDARDYM  = @iCalYmFr
					   AND A.CUSTCD      = @w_CustCd
					   AND A.ItemGrpCd   = @iItemGrpCd
					UNION ALL 
					SELECT ''                               AS ITEMNO     --품목코드
						   ,'지원금'                        AS ITEMNM     --품목명
						   ,''                              AS SPEC       --규격
						   ,''                              AS UNIT       --단위
						   ,0                              AS OUTQTY     --수량
						   ,0                              AS SALESPRICE --단가     
						   ,SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1  AS AMT        --지원금
						   ,2                            AS SEQ
					  FROM MISTO..TORentCalcHistory A
					 WHERE A.STANDARDYM  = @iCalYmFr
					   AND A.CUSTCD      = @w_CustCd
					   AND A.ItemGrpCd   = @iItemGrpCd
					   AND CONVERT(MONEY,ISNULL(A.SupportAmt,'0')) <> 0
                    ) X
					WHERE ISNULL(X.AMT,0)  <> 0

				/*합계*/
				SELECT SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
				        + (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)            AS SUPPLYAMT --공급가액
				       ,ROUND((SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
				        + (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)) * 0.1,0)  AS VAT       --부가세
					   ,(SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
				        + (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)
						) + 
						ROUND((SUM(ISNULL(CONVERT(MONEY,ISNULL(A.RENTAMT,'0')),0))
				        + (SUM(ISNULL(CONVERT(MONEY,ISNULL(A.SupportAmt,'0')),0)) * -1)) * 0.1,0)   AS AMT        --금액
				  FROM MISTO..TORentCalcHistory A
				 WHERE A.STANDARDYM  = @iCalYmFr
				   AND A.CUSTCD      = @w_CustCd
				   AND A.ItemGrpCd   = @iItemGrpCd
			END
		IF @iKind IN ('02') --판매			
			BEGIN
			    /*납품정보*/
			    SELECT MISTW.DBO.fn_gtcEquipNm(A.EquipCustCd) 
				      +'/'+ MISTW.DBO.fn_gtcSiteNm(A.SiteCd)  AS DELEVERY --납품처(설비업체+현장+화물지점)
				     , A.TELNO     AS TELNO     --전화번호
					 , ''          AS FAXNO     --전화번호2
					 , MISTW.DBO.fn_gtcDPNm(A.DeliveryPlace)          AS ADDR      --주소
					 , ''          AS CONSIGNEE --담당자  A.CONSIGNEE 
					 , ''          AS MOBILENO  --CP번호
					 , A.DeliveryNo AS OUTNO		--출고번호
					 , MISCM.DBO.fn_gfnDateTypeTrans(DeliveryYmd,'-') AS OUTYMD	--출고일자
				FROM MISTO..TODeliveryMaster A
				WHERE DeliveryNo = @iDeliveryNo
			   
			    /*품목리스트*/
				SELECT  B.ClassCd                    AS ITEMNO     --품목코드
				       ,C.ClassNm                    AS ITEMNM     --품목명
					   ,C.Spec                       AS SPEC       --규격
					   ,C.Unit                       AS UNIT       --단위
					   ,ISNULL(B.Qty,0)                        AS OUTQTY     --수량
					   ,ISNULL(B.Price,0)           AS SALESPRICE  --단가  
					   ,ISNULL(B.Price,0)*B.Qty     AS AMT         --금액
					   ,'' AS REMARK
					   ,B.LotNo                     AS LOTNO
					   ,SEQ
				  FROM MISTO..TODeliveryMaster A
				  INNER JOIN MISTO..TODeliveryDetail B
				    ON A.DeliveryNo = B.DeliveryNo
				   AND B.ItemGrpCd =@iItemGrpCd
				  INNER JOIN MISTO..TOClass C
				    ON B.ClassCd = C.ClassCd 
					AND C.ItemGrpCd = @iItemGrpCd	 
				 WHERE A.DeliveryNo = @iDeliveryNo
				   AND A.ItemGrpCd  = @iItemGrpCd

				/*합계*/
				SELECT SUM(ISNULL(B.SUPAMT,0)) AS SUPPLYAMT --공급가액
				      ,SUM(ISNULL(B.VAT,0))    AS VAT       --부가세
					  ,SUM(ISNULL(B.Amt,0))    AS AMT       --금액
				  FROM MISTO..TODeliveryMaster A
				  INNER JOIN MISTO..TODeliveryDetail B
				    ON A.DeliveryNo = B.DeliveryNo
				  AND B.ItemGrpCd =@iItemGrpCd
				  INNER JOIN MISTO..TOClass C
				    ON B.ClassCd = C.ClassCd 
					AND C.ItemGrpCd = @iItemGrpCd	 
				 WHERE A.DeliveryNo = @iDeliveryNo
				   AND A.ItemGrpCd  = @iItemGrpCd
			END
		IF @iKind IN ('05') --공구연체료
			BEGIN
			    /*납품정보*/
				SELECT   ''  AS DELEVERY --납품처(설비업체+현장+화물지점)
						,A.TelNo                     AS TELNO     --전화번호
						,''                          AS FAXNO     --전화번호2
						,A.Address1+' '+A.Address2   AS ADDR      --주소
						,''          AS CONSIGNEE --담당자  A.Consignee 
						,''          AS MOBILENO  --CP번호
						,''     AS OUTNO		--출고번호
						,MISCM.dbo.fn_gfnDateTypeTrans(C.PreDate,'-')	         AS OUTYMD		--출고일자
				  FROM MISSA..SACust A
			INNER JOIN MISTO..tbASLate B
					ON A.CustCd =B.CustCd
			INNER JOIN MISTO..tbASLateDtl C
					ON B.AsLateMgmNo = C.AsLateMgmNo
				   AND SEQ = @iSEQ
					WHERE A.CustCd = @w_CustCd

                /*품목리스트*/
				SELECT ClassCd                           AS ITEMNO     --품목코드
					  ,C.ToolNm                AS ITEMNM     --품목명
					  ,C.Spec                           AS SPEC       --규격
					  ,C.Unit                           AS UNIT       --단위
					  ,0                           AS OUTQTY     --수량
					  ,0                           AS SALESPRICE --단가  
					  , ISNULL(B.RealLateFee,0)        AS AMT        --공구연체료
					  , MISTW.DBO.fn_gtcEquipNm(D.EquipCustCd) 
				      +'/'+ MISTW.DBO.fn_gtcSiteNm(D.SiteCd)  AS REMARK --납품처(설비업체+현장+화물지점)
				FROM MISTO..tbASLate A
				INNER JOIN MISTO..tbASLateDtl B
				   ON A.AsLateMgmNo = B.AsLateMgmNo 
				   AND B.ItemGrpCd =@iItemGrpCd
			     INNER JOIN MISTO..TOToolMaster C
				    ON B.LotNo = C.LotNo 
					AND C.ItemGrpCd = @iItemGrpCd	
				INNER JOIN MISTO..TODeliveryMaster D 
					ON B.DELIVERYno = D.DELIVERYno
				WHERE A.CalcYm    = @iCalYmFr
				  AND A.ItemGrpCd = @iItemGrpCd
				  AND A.CUSTCD    = @w_CustCd

				
				/*합계*/
				SELECT SUM(ISNULL(B.RealLateFee,0))           AS SUPPLYAMT --공급가액
					 , SUM(ISNULL(B.RealLateFee,0)) * 0.1     AS VAT       --부가세
					 , SUM(ISNULL(B.RealLateFee,0)) 
					   + (SUM(ISNULL(B.RealLateFee,0)) * 0.1) AS AMT       --금액		
				FROM MISTO..tbASLate A
				INNER JOIN MISTO..tbASLateDtl B
				   ON A.AsLateMgmNo = B.AsLateMgmNo 
				   AND B.ItemGrpCd =@iItemGrpCd
				WHERE A.CalcYm    = @iCalYmFr
				  AND A.ItemGrpCd = @iItemGrpCd
				  AND A.CUSTCD    = @w_CustCd

			END
		IF @iKind IN ('06') --AS청구비
			BEGIN
			    /*납품정보*/
				
				SELECT MISTW.DBO.fn_gtcEquipNm(A.EquipCustCd) 
				      +'/'+ MISTW.DBO.fn_gtcSiteNm(A.SiteCd)  AS DELEVERY --납품처(설비업체+현장+화물지점)
				     , A.TELNO     AS TELNO     --전화번호
					 , ''          AS FAXNO     --전화번호2
					 , MISTW.DBO.fn_gtcDPNm(A.DeliveryPlace)          AS ADDR      --주소
					 , ''          AS CONSIGNEE --담당자  A.CONSIGNEE 
					 , ''          AS MOBILENO  --CP번호
					 , A.DeliveryNo AS OUTNO		--출고번호
					 , MISCM.DBO.fn_gfnDateTypeTrans(D.SubmitYMD ,'-') AS OUTYMD	--출고일자
				FROM MISTO..TODeliveryMaster A
				INNER JOIN MISTO..TOAs B
				ON A.DeliveryNo = B.DeliveryNo 
				  AND B.Seq = @iSEQ
				INNER JOIN MISTO..TOAsFault D
			    ON B.ASNO =  D.ASNO
				AND D.SubmitYn = 'Y'				
				WHERE A.DeliveryNo = @iDeliveryNo
				 

                /*품목리스트*/
				SELECT  ISNULL(X.ITEMNO,'')       AS ITEMNO     --품목코드
				       ,ISNULL(X.ITEMNM,'')       AS ITEMNM     --품목명
					   ,ISNULL(X.SPEC,'')         AS SPEC       --규격
					   ,ISNULL(X.UNIT,'')         AS UNIT       --단위
					   ,ISNULL(X.OUTQTY,0)        AS OUTQTY     --수량
					   ,ISNULL(X.SALESPRICE,0)    AS SALESPRICE --단가
					   ,ISNULL(X.AMT,0)           AS AMT        --임대료
					   ,ISNULL(X.Remark ,'')      AS REMARK
					   ,ISNULL(LOTNO,'')          AS LOTNO
				  FROM
				  (SELECT  E.PartCd                  AS ITEMNO     --품목코드
						  ,F.PARTNM                  AS ITEMNM     --품목명
						  ,F.Spec                    AS SPEC       --규격
						  ,F.Unit                    AS UNIT       --단위
						  ,ISNULL(E.Qty,0)           AS OUTQTY     --수량
						  ,ISNULL(E.Price,0)         AS SALESPRICE --단가  
						,  ISNULL(E.SupAmt,0)        AS AMT        --AS 청구비
						,  a.Remark
						, LotNo
					FROM MISTO..TOAsFault  A
					INNER JOIN MISTO..TOAs C
					   ON A.AsNo = C.AsNo 
					  AND A.ItemGrpCd =@iItemGrpCd
					INNER JOIN MISTO..TODeliveryMaster B
					   ON C.DeliveryNo           = B.DeliveryNo
					   AND B.ItemGrpCd  =  @iItemGrpCd
					INNER JOIN MISSA..SACust D
					   ON B.CUSTCD   = D.CustCd 
					INNER JOIN MISTO..TOAsPart E
					   ON A.AsNo = E.AsNo 
					   AND A.Seq = E.Seq 
					   AND E.ItemGrpCd  = @iItemGrpCd
					INNER JOIN MISTO..TOAsPartMaster F
				       ON E.PartCd = F.PartCd
					  AND F.ItemGrpCd =@iItemGrpCd
					WHERE C.ItemGrpCd   = @iItemGrpCd   --'SP'
					  AND A.SubmitYn    = 'Y'           --청구여부가 'Y' 인 건
					  AND B.DeliveryNo  = @iDeliveryNo
					  AND C.Seq = @iSEQ
					  --AND C.InYmd BETWEEN @iInYmdFr AND @iInYmdTo
					  --AND C.CustCD = @w_CustCd					  
					)X
                   --WHERE ISNULL(X.AMT,0) <> 0

				/*합계*/
				SELECT ISNULL(SUPPLYAMT,0) AS SUPPLYAMT --공급가액
					 , ISNULL(VAT, 0)       AS VAT       --부가세
					 , ISNULL(AMT, 0)       AS AMT       --금액
				  FROM
					(SELECT SUM(ISNULL(E.SupAmt,0))            AS SUPPLYAMT --공급가액
						 , SUM(ISNULL(E.SupAmt,0)) * 0.1      AS VAT       --부가세
						 , SUM(ISNULL(E.SupAmt,0)) + SUM(ISNULL(E.SupAmt,0)) * 0.1            AS AMT       --금액
					FROM MISTO..TOAsFault  A
					INNER JOIN MISTO..TOAs C
					   ON A.AsNo = C.AsNo 
					   AND A.ItemGrpCd =@iItemGrpCd
					INNER JOIN MISTO..TODeliveryMaster B
					   ON C.DeliveryNo           = B.DeliveryNo
					   AND B.ItemGrpCd  =  @iItemGrpCd
					INNER JOIN MISSA..SACust D
					   ON B.CUSTCD               = D.CustCd 
					INNER JOIN MISTO..TOAsPart E
					   ON A.AsNo = E.AsNo 
					   AND A.Seq = E.Seq 
					   AND E.ItemGrpCd  =  @iItemGrpCd
					WHERE C.ItemGrpCd   = @iItemGrpCd   --'SP'
					  AND A.SubmitYn    = 'Y'           --청구여부가 'Y' 인 건
					  AND B.DeliveryNo  = @iDeliveryNo
					    AND C.Seq = @iSEQ
					  --AND C.InYmd BETWEEN @iInYmdFr AND @iInYmdTo
					  AND C.CustCD = @w_CustCd
				 )X 
				 WHERE SUPPLYAMT <> 0
			END
		IF @iKind IN ('07') --분실청구
			BEGIN
			    /*납품정보*/
				SELECT DISTINCT 
				      MISTW.DBO.fn_gtcEquipNm(A.EquipCustCd) 
				      +'/'+ MISTW.DBO.fn_gtcSiteNm(A.SiteCd)  AS DELEVERY --납품처(설비업체+현장+화물지점)
				     , A.TELNO     AS TELNO     --전화번호
					 , ''          AS FAXNO     --전화번호2
					 , MISTW.DBO.fn_gtcDPNm(A.DeliveryPlace)          AS ADDR      --주소
					 , ''          AS CONSIGNEE --담당자  A.CONSIGNEE 
					 , ''          AS MOBILENO  --CP번호
					 , A.DeliveryNo AS OUTNO		--출고번호
					 , MISCM.DBO.fn_gfnDateTypeTrans(B.LOSSYMD,'-')   AS OUTYMD	--출고일자
				FROM MISTO..TODeliveryMaster A
				INNER JOIN MISTO..ToLoss B
				ON A.DeliveryNo = B.DeliveryNo
				WHERE A.DeliveryNo = @iDeliveryNo
				ORDER BY OUTYMD DESC

				/*품목리스트*/
				SELECT  ''                        AS ITEMNO     --품목코드
						,ISNULL(X.ITEMNM,'')       AS ITEMNM     --품목명
						,ISNULL(X.SPEC,'')         AS SPEC       --규격
						,ISNULL(X.UNIT,'')         AS UNIT       --단위
						,ISNULL(X.OUTQTY,0)        AS OUTQTY     --수량
						,ISNULL(X.SALESPRICE,0)    AS SALESPRICE --단가
						,ISNULL(X.AMT,0)           AS AMT        --임대료
						,LOTNO                     AS LOTNO
						,'' AS REMARK
					FROM
					(SELECT MISTW.DBO.fn_gtcToolNm('2', A.ItemGrpCd ,A.LotNo)              AS ITEMNM     --품목명
							,MISTW.DBO.fn_gtcToolSpec('2', A.ItemGrpCd,A.LotNo)          AS SPEC       --규격
							,'EA'                          AS UNIT       --단위
							,1                           AS OUTQTY     --수량
							,ISNULL(A.Amt,0)                       AS SALESPRICE --단가  
							,ISNULL(A.Amt,0)         AS AMT        --공구분실청구비
							,a.LotNo
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
						ON A.DeliveryNo           = B.DeliveryNo 
						AND B.ItemGrpCd =@iItemGrpCd
					INNER JOIN MISSA..SACust C
						ON B.CUSTCD               = C.CustCd 
					WHERE A.LossYmd BETWEEN @iInYmdFr AND @iInYmdTo  
						AND A.ItemGrpCd            = @iItemGrpCd
						AND A.AMT                  <> 0
						AND B.CustCd               = @w_CustCd
						AND A.DeliveryNo           = @iDeliveryNo
                    )X

                /*합계*/
					SELECT ISNULL(M.AMT,0)                                   AS SUPPLYAMT --공급가액
						,  ROUND((ISNULL(M.AMT,0))*0.1,0)  AS VAT     
						,  (ISNULL(M.AMT,0) + ROUND((ISNULL(M.AMT,0))*0.1,0)) AS AMT 
					FROM
					(SELECT SUM(ISNULL(A.Amt,0))         AS AMT        --공구분실청구비
					FROM MISTO..ToLoss A
					INNER JOIN MISTO..TODeliveryMaster B
						ON A.DeliveryNo           = B.DeliveryNo 
						AND B.ItemGrpCd =@iItemGrpCd
					INNER JOIN MISSA..SACust C
						ON B.CUSTCD               = C.CustCd 
					WHERE A.LossYmd BETWEEN @iInYmdFr AND @iInYmdTo  
						AND A.ItemGrpCd            = @iItemGrpCd
						AND A.AMT                  > 0
						AND B.CustCd               = @w_CustCd
						AND A.DeliveryNo           = @iDeliveryNo 
					)M
			END
		IF @iKind IN ('08') --분실취소			
			BEGIN
			SELECT DISTINCT 
				      MISTW.DBO.fn_gtcEquipNm(A.EquipCustCd) 
				      +'/'+ MISTW.DBO.fn_gtcSiteNm(A.SiteCd)  AS DELEVERY --납품처(설비업체+현장+화물지점)
				     , A.TELNO     AS TELNO     --전화번호
					 , ''          AS FAXNO     --전화번호2
					 , MISTW.DBO.fn_gtcDPNm(A.DeliveryPlace)          AS ADDR      --주소
					 , ''          AS CONSIGNEE --담당자  A.CONSIGNEE 
					 , ''          AS MOBILENO  --CP번호
					 , A.DeliveryNo AS OUTNO		--출고번호
					 , MISCM.DBO.fn_gfnDateTypeTrans(CancelYmd,'-')   AS OUTYMD	--출고일자
				FROM MISTO..TODeliveryMaster A
				INNER JOIN MISTO..ToLoss B
				ON A.DeliveryNo = B.DeliveryNo
				WHERE A.DeliveryNo = @iDeliveryNo
				  AND ISNULL (CancelYmd ,'')<>''
				  ORDER BY OUTYMD DESC 


				SELECT  ''                        AS ITEMNO     --품목코드
						,ISNULL(X.ITEMNM,'')       AS ITEMNM     --품목명
						,ISNULL(X.SPEC,'')         AS SPEC       --규격
						,ISNULL(X.UNIT,'')         AS UNIT       --단위
						,ISNULL(X.OUTQTY,0)        AS OUTQTY     --수량
						,ISNULL(X.SALESPRICE,0)    AS SALESPRICE --단가
						,ISNULL(X.AMT,0)           AS AMT        --임대료
						,'' AS REMARK
						,LOTNO                     AS LOTNO
					FROM
				(SELECT MISTW.DBO.fn_gtcToolNm('2', A.ItemGrpCd ,A.LotNo)              AS ITEMNM     --품목명
                    ,MISTW.DBO.fn_gtcToolSpec('2', A.ItemGrpCd,A.LotNo)          AS SPEC       --규격
					,'EA'                        AS UNIT       --단위
					,1                           AS OUTQTY     --수량
					,ISNULL(A.Amt,0) * -1        AS SALESPRICE --단가  
					,ISNULL(A.Amt,0) * -1        AS AMT        --공구분실청구비
					,LotNo 
				FROM MISTO..ToLoss A
				INNER JOIN MISTO..TODeliveryMaster B
					ON A.DeliveryNo           = B.DeliveryNo
					AND B.ItemGrpCd =@iItemGrpCd 
				INNER JOIN MISSA..SACust C
					ON B.CUSTCD               = C.CustCd 
				WHERE A.CancelYmd BETWEEN @iInYmdFr AND @iInYmdTo  						  
					AND A.ItemGrpCd            = @iItemGrpCd
					AND A.AMT                  <> 0
					AND B.CustCd               = @w_CustCd
					AND A.DeliveryNo           = @iDeliveryNo 
				)X
					
				/*합계*/
				SELECT ISNULL(N.AMT,0)                 AS SUPPLYAMT --공급가액
					,  ROUND(ISNULL(N.AMT,0)*0.1,0)  AS VAT     
					,  ISNULL(N.AMT,0) + ROUND(ISNULL(N.AMT,0)*0.1,0)AS AMT 
				FROM				 
				(SELECT SUM(ISNULL(A.Amt,0)) * -1        AS AMT        --공구취소청구비
				FROM MISTO..ToLoss A
				INNER JOIN MISTO..TODeliveryMaster B
					ON A.DeliveryNo           = B.DeliveryNo 
					AND B.ItemGrpCd =@iItemGrpCd
				INNER JOIN MISSA..SACust C
					ON B.CUSTCD               = C.CustCd 
				WHERE A.CancelYmd BETWEEN @iInYmdFr AND @iInYmdTo  					  
					AND A.ItemGrpCd            = @iItemGrpCd
					AND A.AMT                  > 0
					AND B.CustCd               = @w_CustCd
					AND A.DeliveryNo           = @iDeliveryNo 
				)N	
				 
			END
	END


	
END
```

<a id="mistw-proc-combasket-a-01"></a>
## `MISTW.dbo.proc_comBasket_A_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_comBasket_A_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_comBasket_A_01.sql

```sql

/********************************************************************************
제    목 : 주문하기
프로그램 : 주문등록
등 록 일 : 2018-04-16
등 록 자 : 서도종
수정일			수정자				내용
-----------------------------------------------------------------------
2022-01-27		안진주				공구 임대주문건 관련하여 준공예정일 추가요청으로 인한 준공예정일 COLUMN생성 후 파라미터 및 값 추가
2022-12-14		안진주				준공일자(공구회수예정일) 운영기 적용작업 진행
2023-02-16		안진주				주문접수처리가 아닌 등록시점에는 해당 일자가 비어있을 수 있게 처리

[임대공구]
EXEC MISTW..proc_comBasket_A_01 @iGUBUN='A',@iOrdEnrNo=NULL,@iITEMGRPCD='SP',@iORDGB='SP03',@iOrdType='01',@iCUSTCD='10037',@iEquipCustNm='설비업체명',@iSite='현장명',@iDeliveryPlace='강남구 역삼동',@iEquipCustCd=''
,@iSiteCd='',@iDeliveryPlaceCd='',@iDeliveryExpectYmd='',@iTelNo='02-574-6421',@iConsignee='구우정보',@iAdr='강남구 역삼동 레트로 빌딩',@iDeliveryCD='01',@iOrdRmk='빨리가져다 주세요',@iOrdEmpNo='99999',@iOrdRecEmpNo='',@iInputGb='01'
,@iOrdEmpRmk='',@iAsAplOutNo='',@iBeffMovOrdEnrNo=NULL,@iBefMovContractNo='',@iBefMovDeliveryNo='',@iPGNO='0',@iOutStat='01',@iLOGINID='99999'
 
       EXEC  MISTW.dbo.proc_comBasket_A_01  'A' , '' , 'GP' , 'GP04' , '01' , 'AA006' , '' , '세검정초등학교' , '서울시 종로구 신영동 219-4' , '3491' , '05279' 
   , '03032' , '20180911' , '' , '010-8457-0357' , '' , '' , '' , '' , null , '' 
   , '01' , null , 'TD20180900024' , '' , 'TC20180900045' , 'TD20180900024' , '01' , 'WGTA002' , 'AA00601' 
[AS신청]
       EXEC  MISTW.dbo.proc_comBasket_A_01  'A' , '' , 'SP' , 'SP04' , '01' , 'AA007' , '' , '계명-쌍림동' , '구의동강변SKVIEW신축(SK)' , '00729' , '00675' 
   , '00747' , '' , '' , '01090217099' , '' , '' , '' , '' , '99999' , '' 
   , '01' , '' , '20170623001' , '' , '' , '' , '01' , 'WGTA002' , 'AA007' 

[현장이동신청]
EXEC MISTW..proc_comBasket_A_01 @iGUBUN='A',@iOrdEnrNo=NULL,@iOrdEnrNo=NULL,@iITEMGRPCD='SP',@iORDGB='SP05',@iOrdType='01',@iCUSTCD='AA007',@iEquipCustNm='',@iSite='',@iDeliveryPlace='',@iEquipCustCd='2001'
,@iSiteCd='03197',@iDeliveryPlaceCd='01137',@iDeliveryExpectYmd='',@iTelNo='010-3726-4945',@iConsignee='구우정보',@iAdr='',@iDeliveryCD='',@iOrdRmk='현장이동신청이요 빨리가져다 주세요',@iOrdEmpNo='99999',@iOrdRecEmpNo='',@iInputGb='01'
,@iOrdEmpRmk='',@iAsAplOutNo='',@iBeffMovOrdEnrNo=1000040,@iBefMovContractNo='',@iBefMovDeliveryNo='',@iOutStat='01',@iPGNO='0',@iLOGINID='99999'

[대리점이관]
EXEC MISTW..proc_comBasket_A_01 @iGUBUN='A',@iOrdEnrNo=NULL,@iITEMGRPCD='SP',@iORDGB='SP05',@iOrdType='01',@iCUSTCD='AA007',@iEquipCustNm='',@iSite='',@iDeliveryPlace='',@iEquipCustCd='2001'
,@iSiteCd='03197',@iDeliveryPlaceCd='01137',@iDeliveryExpectYmd='',@iTelNo='010-3726-4945',@iConsignee='구우정보',@iAdr='',@iDeliveryCD='',@iOrdRmk='현장이동신청이요 빨리가져다 주세요',@iOrdEmpNo='99999',@iOrdRecEmpNo='',@iInputGb='01'
,@iOrdEmpRmk='',@iAsAplOutNo='',@iBeffMovOrdEnrNo=1000040,@iBefMovContractNo='',@iBefMovDeliveryNo='',@iOutStat='01',@iPGNO='0',@iLOGINID='99999'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_comBasket_A_01]
	 @iGUBUN           varchar(1)	    	--A저장,D삭제,U수정 FLAG 
	,@iOrdEnrNo        BIGINT               --주문등록번호(수정/삭제시 들어옴) 
	,@iITEMGRPCD       VARCHAR(2)	    	--품목군코드(SP,GP로 들어옴) 	
	,@iORDGB           VARCHAR(5)	    	--주문구분
	,@iOrdType         VARCHAR(5)	    	--주문유형 (01:정상,02:반품)
	,@iCUSTCD          VARCHAR(5)	    	--거래처코드	
	,@iEquipCustNm     varchar(100)         --설비업체명
	,@iSite            varchar(100)         --현장명
	,@iDeliveryPlace   varchar(100)         --화물지점명
	,@iEquipCustCd     varchar(5)           --설비업체코드
	,@iSiteCd          varchar(5)           --현장코드
	,@iDeliveryPlaceCd varchar(5)           --화물지점코드
	,@iDeliveryExpectYmd varchar(10)         --출고예정일
	,@iSpecialRecord   varchar(1000)        --특이사항
	,@iTelNo           varchar(20)          --전화번호
	,@iConsignee       varchar(50)          --인수자
	,@iAdr			   varchar(200)         --주소
	,@iDeliveryCD      varchar(15)          --배송방법      
	,@iOrdRmk          varchar(1000)        --주문비고      
	,@iOrdEmpNo        varchar(10)          --주문자  
	,@iOrdRecEmpNo     varchar(10)          --주문접수자(CS 에서 입력시에만 들어옴)
	,@iInputGb         varchar(15)          --입력구분  	    
	,@iOrdEmpRmk       varchar(1000)        --주문등록자비고
	,@iAsAplOutNo      varchar(50)          --AS신청출고번호(사용안함)
	,@iBeffMovOrdEnrNo BIGINT               --화면에서 선택된 건의 주문등록번호 
    ,@iBefMovContractNo  varchar(20)        --이동전계약번호    
	,@iBefMovDeliveryNo  varchar(20)        --이동전출고번호    
	,@iOutStat	       VARCHAR(5)        	--출고상태
	,@iPGNO            VARCHAR(20)      	--프로그램번호
	,@iLOGINID         VARCHAR(10)       	--로그인사번
	,@iOutWithYN	   varchar(1) = 'N'     --제품출고건과출고요청
	,@iCOMPLTDT		   VARCHAR(8)=''		--준공일자(공구회수예정일)
	,@iTaxGb		   varchar(2) = '11'    --과세구분
		
AS
DECLARE @w_ErrYN						VARCHAR(1)
	  , @w_ErrMsg						VARCHAR(1000)
	  , @o_ComNo						VARCHAR(13)
	  , @w_OrdYMD						VARCHAR(8)
	  , @w_TaxGb						VARCHAR(15)
	  , @w_OrdEnrNo						INT
	  , @w_Cnt							INT
	  , @w_DeliveryExpectYmd			VARCHAR(8)
	  , @o_ErrYn						VARCHAR(1)
	  
BEGIN
	SET NOCOUNT ON;
		-- 초기화
	SET @w_ErrYN = 'N'
	SET @w_ErrMsg = '정상 처리되었습니다.'

	SET NOCOUNT ON;

	/*주문하기*/
	
	IF @iGUBUN = 'A'
		BEGIN

			/*주문일자*/
			SELECT @w_OrdYMD = CONVERT(VARCHAR,GETDATE(),112)    

			/*주문번호채번01:물류주문,02:물류출고,03:공구주문,04:구매발주,05:구매입고*/
			EXEC proc_GetcomNo_S '03', @w_OrdYMD, @o_ComNo OUTPUT	

			/*과세구분*/
			--SELECT @w_TaxGb = ISNULL(TAXGB,'*') FROM MISSA.DBO.SACust WHERE CustCd=@iCUSTCD
			SELECT @w_TaxGb = @iTaxGb

			/*kEY 채번*/
			SELECT @w_OrdEnrNo = NEXT VALUE FOR seq_GtcSequence

			/*출고예정일 이동일자가 1일이 아닌경우 익월 1일로 자동 셋팅*/

			IF RIGHT(@iDeliveryExpectYmd,2) <> '01' AND @iORDGB IN ('SP05','SP06','GP05','GP06')
				BEGIN
					SET @w_DeliveryExpectYmd = CONVERT(VARCHAR(6),DATEADD(M,1,CONVERT(DATETIME,@iDeliveryExpectYmd)),112)+'01' 
				END
			ELSE
				BEGIN
					SET @w_DeliveryExpectYmd = NULL
				END

			BEGIN
			  INSERT INTO MISTW..tbGtcOrdEnr
					(OrdEnrNo              --주문등록번호  
					,OrdNo                 --주문번호      
					,OrdGB                 --주문구분      
					,OrdType               --주문유형      
					,CustCd                --거래처코드    
					,OrdDte                --주문일시      
					,ItemGrpCd             --품목군
					,EquipCustNm           --설비업체      
					,Site                  --현장          
					,DeliveryPlace         --화물지점      
					,TelNo                 --전화번호      
					,Consignee             --인수자        
					,Adr                   --주소          
					,DeliveryCD            --배송방법      
					,OrdRmk                --주문비고      
					,OrdEmpNo              --주문자
					,CanCelYn              --취소여부      
					,OrdRecDte             --주문접수일시  
					,OrdRecEmpNo           --주문접수자    
					,OrdCanCelEmpNo        --주문취소자    
					,OrdCanCelDte          --주문취소일시  
					,InputGb               --입력구분      
					,OrdEmpRmk             --주문등록자비고
					,OutStat               --출고상태      
					,ContractOKYn          --계약서확인여부			
					,AsAplOutNo            --AS신청출고번호
					,EquipCustCd           --설비업체코드
					,SiteCd                --현장코드
					,DeliveryPlaceCd       --화물지점코드
					,CloseYn               --종결여부 
					,TaxGb                 --과세구분 
					,DeliveryExpectYmd     --출고예정일
					,MoveMgmNo             --공구이동관리번호
					,SpecialRecord         --특이사항 
					,OutWithYN			   --제품출고건과출고요청
					,LineColor             --라인색상      
					,LineMemo              --라인메모      
					,PgNo                  --프로그램번호  
					,AddEmpNo              --등록자        
					,AddDt                 --등록일        
					,UpdEmpNo              --수정자        
					,UpdDt                 --수정일     
					,CompltDt			   --준공예정일
				  )
			  VALUES
				   ( @w_OrdEnrNo             --주문등록번호 
					,@o_ComNo                --주문번호      
					,@iORDGB                 --주문구분      
					,@iOrdType               --주문유형      
					,@iCustCd                --거래처코드    
					,GETDATE()               --주문일시      
					,LEFT(@iORDGB,2)         --품목군
					,@iEquipCustNm           --설비업체      
					,@iSite                  --현장          
					,@iDeliveryPlace         --화물지점      
					,@iTelNo                 --전화번호      
					,@iConsignee             --인수자        
					,@iAdr                   --주소          
					,@iDeliveryCD            --배송방법      
					,@iOrdRmk                --주문비고      
					,@iOrdEmpNo              --주문자        
					,'N'                     --취소여부      
					--,CASE WHEN @iOrdRecEmpNo <> '' THEN GETDATE()
					--	  ELSE '' 
					-- END		             --주문접수일시  
					,NULL					 --20230216주문등록시점에 접수일시는 현재날짜로 등록될 수 있도록
					,@iOrdRecEmpNo           --주문접수자    
					,''                      --주문취소자    
					,NULL                    --주문취소일시  
					,@iInputGb               --입력구분(01:WEB,02:CS)      
					,''                      --주문등록자비고
					,@iOutStat               --출고상태(01	접수대기,02	접수완료,03	출고진행,04	출고완료,99	주문취소)      
					,'N'                     --계약서확인여부
					,@iAsAplOutNo            --AS신청출고번호
					,@iEquipCustCd           --설비업체코드
					,@iSiteCd                --현장코드
					,@iDeliveryPlaceCd       --화물지점코드
					,'N'                     --종결여부  
					,@w_TaxGb                --과세구분
					,@w_DeliveryExpectYmd    --출고예정일
					,0                       --공구이동관리번호 
					,@iSpecialRecord         --특이사항 
					,@iOutWithYN			 --제품출고건과출고요청    			    
					,'*'                     --라인색상      
					,'*'                     --라인메모      
					,@iPGNO                  --프로그램번호  
					,@iLOGINID               --등록자        
					,GETDATE()               --등록일        
					,@iLOGINID               --수정자        
					,GETDATE()               --수정일
					,@iCOMPLTDT				 --준공예정일
				 )


				 IF @@ROWCOUNT <= 0 
					BEGIN
		  			  SET @w_ErrYN = 'Y'
		 			  SET @w_ErrMsg = '주문등록시 오류가 발생하였습니다'
					  GOTO ENDSTEP
					END
				 /*아래 로직은 proc_GTCC001_A_06 프로시져에서 처리함*/
				 --IF @iORDGB  IN ('SP02','GP02')
					--BEGIN
					--	EXEC proc_comBasket_A_04 'A',@iITEMGRPCD,@iORDGB,@w_OrdEnrNo,@iPGNO,@iLOGINID,@o_ErrYn OUTPUT

					--	-- @iGUBUN                varchar(1)	--A저장,D삭제,U수정 FLAG 
					--	--,@iITEMGRPCD            VARCHAR(2)	--품목군코드(SP,GP로 들어옴)
					--	--,@iORDGB                VARCHAR(5)	--주문구분(SP03,GP03,SP02,GP02)	
					--	--,@iOrdEnrNo             int         --주문등록번호 	
					--	--,@iPGNO                 VARCHAR(20) --프로그램번호
					--	--,@iLOGINID              VARCHAR(10) --로그인사번
					--	--,@iERRYN                VARCHAR(1) OUTPUT
					--END 

				IF @iORDGB  IN ('SP05','GP05','SP06','GP06')
					BEGIN
		
						EXEC proc_comBasket_A_05 'A',@iITEMGRPCD,@iORDGB,@w_OrdEnrNo,@iBeffMovOrdEnrNo ,@iBefMovContractNo ,@iBefMovDeliveryNo,@iPGNO,@iLOGINID,@o_ErrYn OUTPUT


		
						-- @iGUBUN                varchar(1)	 --A저장,D삭제,U수정 FLAG 
						--,@iITEMGRPCD            VARCHAR(2)	 --품목군코드(SP,GP로 들어옴)
						--,@iORDGB                VARCHAR(5)	 --주문구분(SP05,GP05,'SP06','GP06')	
						--,@iAfMovOrdEnrNo        INT          --이동후주문등록번호(새로운 주문등록번호)
						--,@iBeffMovOrdEnrNo      INT          --이동전주문등록번호(이전  주문등록번호)
						--,@iBefMovContractNo     varchar(11)  --이동전계약번호    
						--,@iBefMovDeliveryNo     varchar(11)  --이동전출고번호    	
						--,@iPGNO                 VARCHAR(20)  --프로그램번호
						--,@iLOGINID              VARCHAR(10)  --로그인사번
						--,@iERRYN                VARCHAR(1) OUTPUT
					END 				 
			END
		END
	ELSE IF @iGUBUN = 'U' 	
		BEGIN



			UPDATE MISTW..tbGtcOrdEnr
			   SET   EquipCustNm          	=	@iEquipCustNm           --설비업체                                                               
					,Site                 	=	@iSite                  --현장                                                                   
					,DeliveryPlace        	=	@iDeliveryPlace         --화물지점                                                               
					,TelNo                	=	@iTelNo                 --전화번호                                                               
					,Consignee            	=	@iConsignee             --인수자                                                                 
					,Adr                  	=	@iAdr                   --주소                                                                   
					,DeliveryCD           	=	@iDeliveryCD            --배송방법                                                               
					,OrdEmpNo             	=	@iOrdEmpNo              --주문자                                                                 
					,CanCelYn             	=	'N'                     --취소여부                                                               
					,OrdCanCelEmpNo       	=	''                      --주문취소자                                                             
					,OrdCanCelDte         	=	NULL                    --주문취소일시                                                           
					,InputGb              	=	@iInputGb               --입력구분(01:WEB,02:CS)                                                 
					,EquipCustCd          	=	@iEquipCustCd           --설비업체코드                                                           
					,SiteCd               	=	@iSiteCd                --현장코드                                                               
					,DeliveryPlaceCd      	=	@iDeliveryPlaceCd       --화물지점코드                                                           
					,SpecialRecord        	=	@iSpecialRecord         --특이사항   
					,OutWithYN				=   @iOutWithYN				--제품출고건과출고요청
					,CompltDt				=	@iCOMPLTDT				--준공일자(공구회수예정일)
					,PgNo                 	=	@iPGNO                  --프로그램번호                                                           
					,UpdEmpNo             	=	@iLOGINID               --수정자                                                                 
					,UpdDt                	=	GETDATE()               --수정일    
				WHERE	OrdEnrNo = @iOrdEnrNo

			EXEC MISTW..proc_comBasket_A_08 @iOrdEnrNo=@iOrdEnrNo,@iITEMGRPCD=@iITEMGRPCD,@iORDGB=@iORDGB,@iPGNO=@iPGNO,@iLOGINID=@iLOGINID,@iErrYN=@w_ErrYN OUTPUT, @iErrMsg=@w_ErrMsg OUTPUT

			IF @w_ErrYN = 'Y' BEGIN
				GOTO ENDSTEP
			END					
		END		   
	ELSE IF @iGUBUN = 'D'
		BEGIN
			
			IF @iORDGB  IN ('SP02','GP02')
				BEGIN
					SELECT @w_Cnt= COUNT(*)
					  FROM tbGtcSaleOut
					 WHERE OrdEnrNo = @iOrdEnrNo
					 
					 IF @w_Cnt > 0
						BEGIN
							SET @w_ErrYN = 'Y'
		 					SET @w_ErrMsg = '판매출고건이 있으므로 삭제할 수 없습니다'
							GOTO ENDSTEP
						END

				END
			ELSE IF @iORDGB  IN ('SP03','GP03','SP05','GP05','SP06','GP06') 
				BEGIN
					SELECT @w_Cnt= COUNT(*)
					  FROM tbGtcRentSet
					 WHERE OrdEnrNo = @iOrdEnrNo
					 
					 IF @w_Cnt > 0
						BEGIN
							SET @w_ErrYN = 'Y'
		 					SET @w_ErrMsg = '임대출고건이 있으므로 삭제할 수 없습니다'
							GOTO ENDSTEP
						END

				END

			UPDATE MISTW..tbGtcOrdEnr
			   SET   CanCelYn             	=	'Y'                     --취소여부                                                               
					,OrdCanCelEmpNo       	=	@iOrdEmpNo              --주문취소자                                                             
					,OrdCanCelDte         	=	GETDATE()               --주문취소일시                                                           
					,OutStat              	=	'99'                    --출고상태(01	접수대기,02	접수완료,03	출고진행,04	출고완료,99	주문취소) 
					,CloseYn              	=	'Y'                     --종결여부                                                               
					,UpdEmpNo             	=	@iLOGINID               --수정자                                                                 
					,UpdDt                	=	GETDATE()               --수정일    
				WHERE	OrdEnrNo = @iOrdEnrNo	
		END
END

/****************/
ENDSTEP:
/****************/


	SELECT	@w_ErrYN ERRYN, @w_ErrMsg ERRMSG,  @w_OrdEnrNo ORDENRNO
RETURN
```

<a id="mistw-proc-gtce010-a-01"></a>
## `MISTW.dbo.proc_GTCE010_A_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_GTCE010_A_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_GTCE010_A_01.sql

```sql

/********************************************************************************
제    목 : 패킹정보 저장
프로그램 : 공구 패킹
등 록 일 : 2018-08-16
등 록 자 : 정재광
수정일		수정자		내용
-----------------------------------------------------------------------
20211116	안진주		SP, GP 화물비 [현불택배] 금액 인상 변경처리 요청으로 인한 금액 수정 진행
20221013	안진주		GP화물비 [현불택배] 0원으로 변경처리 요청으로 인한 금액 수정 진행
20221014	안진주		GP화물비 [현불택배] 6000원으로 변경처리 요청으로 인한 금액 수정 진행
20221017	안진주		현불택배 6000원 - 운영기적용일자
20221114	안진주		GP화물비 변경 요청으로 인한 개발기 진행
20230125	고종수		물류, SP화물비 변경 요청으로 인한 개발기 진행(운영기는 20230201 적용)

EXEC proc_GTCE010_A_01 @iAUD = 'U', @ipkgEnrNo = '4628740', @iOrdEnrNo = '4628737', @ipkgNo = 'TK20181000011', @ipkgYmd = '20181002', @iAppointEmpNo = 'A17121101', @iPkgStat = '03', @ipkgWorkRmk = '1111', @iCustCd = 'AA001', @iEquipCustNm = '경민테크', @iSite = '광주지식산업센터', @iDeliveryPlace = '양산시 물금읍 벙어리', @iTelNo = '010-4345-2398', @iAdr = '', @iL_BoxQty = '0', @iM_BoxQty = '0', @iS_BoxQty = '1', @iL_BskQty = '0', @iM_BskQty = '0', @iS_BskQty = '1', @iL_BoxFee = '0', @iM_BoxFee = '0', @iS_BoxFee = '3000', @iL_BskFee = '0', @iM_BskFee = '0', @iS_BskFee = '6000', @iPaletFee = '0', @iPaletQty = '0', @iTransitFee = '9000', @iTransitVat = '900', @iTransitSum = '9900', @ipkgOrdRmk = '', @iDeliveryNo = '', @iPgNo = 'TABLET', @iAddEmpNo = 'ERP18', @iDeliveryCD = '01', @ipkgWorkRmk = '1111'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_GTCE010_A_01]
	 @iAUD				VARCHAR(1)		-- AUD
	,@ipkgEnrNo			BIGINT			-- 패킹등록번호
	,@iOrdEnrNo			BIGINT			-- 주문등록번호
	,@ipkgNo			VARCHAR(15)		-- 패킹번호
	,@ipkgYmd			VARCHAR(8)		-- 패킹일자
	,@iAppointEmpNo		VARCHAR(10)		-- 지정작업자
	,@iPkgStat			VARCHAR(10)		--패킹상태
	,@ipkgWorkRmk		VARCHAR(1000)	-- 패킹작업비고
	,@iCustCd			VARCHAR(10)		-- 거래처코드
	,@iEquipCustNm		VARCHAR(100)	-- 설비업체
	,@iSite				VARCHAR(100)	-- 현장
	,@iDeliveryPlace	VARCHAR(1000)	-- 화물지점
	,@iTelNo			VARCHAR(100)	-- 전화번호
	,@iAdr				VARCHAR(1000)	-- 주소
	,@iL_BoxQty			INT				-- L_BOX수량
	,@iM_BoxQty			INT				-- M_BOX수량
	,@iS_BoxQty			INT				-- S_BOX수량
	,@iL_BoxFee			MONEY			-- L_BOX비용
	,@iM_BoxFee			MONEY			-- M_BOX비용
	,@iS_BoxFee			MONEY			-- S_BOX비용
	,@iL_BskQty			INT				-- L_BSK수량
	,@iM_BskQty			INT				-- M_BSK수량
	,@iS_BskQty			INT				-- S_BSK수량
	,@iL_BskFee			MONEY			-- L_BSK비용
	,@iM_BskFee			MONEY			-- M_BSK비용
	,@iS_BskFee			MONEY			-- S_BSK비용
	,@iPaletFee			MONEY			-- 퍄레트비용	
	,@iPaletQty			int  			-- 퍄레트수량
	,@iTransitFee		MONEY			-- 택배료
	,@iTransitVat		MONEY			-- 택배로부가세
	,@iTransitSum		MONEY			-- 배송료합계
	,@ipkgOrdRmk		VARCHAR(1000)	-- 패킹지시비고
	,@iDeliveryNo		VARCHAR(20)		-- 출고번호
	,@iPgNo				VARCHAR(20)		-- 프로그램번호
	,@iAddEmpNo			VARCHAR(10)		-- 등록자
	,@iDeliveryCD       VARCHAR(10)	   -- 배송방법
	,@iContractNo       VARCHAR(20)	   -- 계약번호
AS
DECLARE @w_ErrYN		VARCHAR(1)
	  , @w_ErrMsg		VARCHAR(1000)
	  , @wpkgEnrNo	    BIGINT		-- 패킹등록번호
	  ,	@wpkgNo		    VARCHAR(15) -- 패킹번호
	  ,	@wGetDate	    VARCHAR(8)	-- 현재날짜
	  , @w_OrdEnrNo     BIGINT
	  , @w_DeliveryCD   VARCHAR(5)
	  , @w_OutGb        VARCHAR(5)
	  , @w_SupplyAmt    MONEY
	  , @w_L_BoxFee     MONEY = 0
	  , @w_M_BoxFee     MONEY = 0
	  , @w_S_BoxFee     MONEY = 0
	  , @w_L_BskFee     MONEY = 0
	  , @w_M_BskFee     MONEY = 0
	  , @w_S_BskFee     MONEY = 0
	  , @w_TransitFee   MONEY = 0
	  , @w_TransitVat   MONEY = 0
	  , @w_TransitSum   MONEY = 0
	  , @wOrdGB         varchar(10) -- 주문구분
	  , @wItemGrpCd     varchar(10) -- 품목군

BEGIN
	SET NOCOUNT ON;
		-- 초기화
	SET @w_ErrYN = 'N'
	SET @w_ErrMsg = '정상 처리되었습니다.'

	SET @wGetDate = CONVERT(VARCHAR(8), GETDATE(), 112)
	SET @wpkgNo = @ipkgNo
	SET @wpkgEnrNo = @ipkgEnrNo

	SELECT @wOrdGB	   = OrdGB
	     , @wItemGrpCd = ItemGrpCd 
	  FROM MISTW..tbGtcOrdEnr
	 WHERE OrdEnrNo = @iOrdEnrNo

	IF @iAUD = 'D' BEGIN
		DELETE FROM tbGtcPkgEnr
		 WHERE pkgEnrNo = @ipkgEnrNo
	END
	ELSE BEGIN

		--패킹완료시 체크로직
		IF @iPkgStat ='03' 
			BEGIN
				IF @wOrdGB IN ('SP02','GP02') 
					BEGIN	--판매
						IF EXISTS(
									SELECT *
									  FROM MISTW..tbGtcOrdEnr A
									  JOIN MISTW..tbGtcOrdEnrDtl B
											ON	B.ordEnrNo = A.OrdEnrNo
											AND	ISNULL(B.OrdItemCanCelYn, 'N') != 'Y'
									  JOIN MISTW..tbGtcSaleOut C
											ON	C.OrdEnrDtlNo = B.OrdEnrDtlNo
											AND	ISNULL(C.OrdItemCanCelYn, 'N') != 'Y'						
									 WHERE A.OrdEnrNo = @iOrdEnrNo
									   AND (	C.Lotno IS NULL
											OR	C.LotnO = ''
											OR	C.Lotno = '*'
											)	) 
							BEGIN
								SET @w_ErrYN = 'Y'
								SET @w_ErrMsg = '패킹완료는 모든 제품번호를 입력해야 가능합니다!'
								GOTO ENDSTEP
							END
					END
				ELSE IF @wOrdGB IN ('SP03','GP03') 
					BEGIN	--임대
						IF EXISTS(
									SELECT *
									  FROM MISTW..tbGtcOrdEnr A
									  JOIN MISTW..tbGtcOrdEnrDtl B	ON	B.ordEnrNo = A.OrdEnrNo AND	ISNULL(B.OrdItemCanCelYn, 'N') != 'Y'
									  JOIN MISTW..tbGtcRentSet C	ON	C.OrdEnrDtlNo = B.OrdEnrDtlNo AND ISNULL(C.OrdItemCanCelYn, 'N') != 'Y'						
									  JOIN MISTO..TOSet D			ON	D.ClassGrpCd = C.ClassGrpCd AND D.ItemGrpCd = SUBSTRING(A.OrdGb, 1,2)
									  JOIN MISTO..TORentSet E		ON	E.ItemGrpCd = D.ItemGrpCd AND E.ClassGrpCd = D.ClassGrpCd
									  JOIN MISTO..TOClass F			ON	F.ItemGrpCd = E.ItemGrpCd AND F.ClassCD = E.ClassCd
									  LEFT JOIN	(	SELECT X.ContractNo, X.ClassCd, Y.Gubun1, Y.Gubun2
													  FROM MISTO..TOContractDetail X 
													  LEFT JOIN MISTO..TOClass Y	ON	Y.ItemGrpCd = X.ItemGrpCd AND Y.ClassCd = X.ClassCd
												) Z
											ON	Z.ContractNo = C.ContractNo
											AND	Z.Gubun1 = F.Gubun1
											AND	Z.Gubun2 = F.Gubun2
									 WHERE A.OrdEnrNo = @iOrdEnrNo
									   AND Z.ContractNo IS NULL	) 
						   
									BEGIN
										SET @w_ErrYN = 'Y'
										SET @w_ErrMsg = '패킹완료는 모든 제품번호를 입력해야 가능합니다!'
										GOTO ENDSTEP
									END
						END
				ELSE IF @wOrdGB IN ('SP04','GP04') 
					BEGIN	--AS				
						IF EXISTS(
									SELECT *
									  FROM MISTW..tbGtcOrdEnr A
									  JOIN MISTW..tbGtcOrdEnrDtl B
											ON	B.ordEnrNo = A.OrdEnrNo
											AND	ISNULL(B.OrdItemCanCelYn, 'N') != 'Y'
									  JOIN MISTW..tbGtcAsApl C
											ON	C.OrdEnrDtlNo = B.OrdEnrDtlNo
											AND	ISNULL(C.OrdItemCanCelYn, 'N') != 'Y'						
									 WHERE A.OrdEnrNo = @iOrdEnrNo
									   AND (	C.Lotno IS NULL
											OR	C.LotnO = ''
											OR	C.Lotno = '*'
											)	) 
							BEGIN
								SET @w_ErrYN = 'Y'
								SET @w_ErrMsg = '패킹완료는 모든 제품번호를 입력해야 가능합니다!'
								GOTO ENDSTEP
							END
					END
			END
		
		/*주문정보에 배송방법 갱신*/
		
		SET @w_DeliveryCD = @iDeliveryCD 
		IF ISNULL(@iDeliveryCD,'') <> '' 		

			BEGIN			
				UPDATE A
					SET DeliveryCD = @iDeliveryCD
					FROM MISTW..tbGtcOrdEnr A
					INNER JOIN MISTW..tbGtcPkgEnr B
					ON A.OrdEnrNo = B.OrdEnrNo 
					AND B.pkgEnrNo = @ipkgEnrNo

				IF @wOrdGB IN ('SP02','GP02') 
					BEGIN	--판매
						UPDATE DM	
						   SET DM.DeliveryCD = @iDeliveryCD
						  FROM MISTW..tbGtcOrdEnr A
						  JOIN MISTO..TODeliveryMaster DM
							ON	DM.DeliveryNo = A.DeliveryNo
						 WHERE A.ordEnrNo = @iOrdEnrNo
					END
				ELSE IF @wOrdGB IN ('SP03','GP03') 
					BEGIN	--임대
						--해당 출고정보의 배송방법 변경
						UPDATE DM	
						   SET DM.DeliveryCD = @iDeliveryCD
						  FROM MISTW..tbGtcOrdEnr A
						  JOIN MISTW..tbGtcOrdEnrDtl B
							ON	B.OrdEnrNo = A.OrdEnrNo
							AND	ISNULL(B.OrdItemCanCelYn, 'N') != 'Y'
						  JOIN MISTW..tbGtcRentSet C
							ON	C.OrdEnrDtlNo = B.OrdEnrDtlNo
							AND	ISNULL(C.OrdItemCanCelYn, 'N') != 'Y'		
						  JOIN MISTO..TODeliveryMaster DM
							ON	DM.DeliveryNo = C.DeliveryNo
						 WHERE A.ordEnrNo = @iOrdEnrNo
				
						----해당 계약정보의 배송방법 변경
						UPDATE CM	
						   SET CM.DeliveryCD = @iDeliveryCD
						  FROM MISTW..tbGtcOrdEnr A
						  JOIN MISTW..tbGtcOrdEnrDtl B
							ON	B.OrdEnrNo = A.OrdEnrNo
							AND	ISNULL(B.OrdItemCanCelYn, 'N') != 'Y'
						  JOIN MISTW..tbGtcRentSet C
							ON	C.OrdEnrDtlNo = B.OrdEnrDtlNo
							AND	ISNULL(C.OrdItemCanCelYn, 'N') != 'Y'		
						  JOIN MISTO..TOContractMaster CM
							ON	CM.ContractNo = C.ContractNo
						 WHERE A.ordEnrNo = @iOrdEnrNo
					END
			END
		
		ELSE
			BEGIN
				--수주번호로 배송방법 가져옴
				SELECT @w_DeliveryCD = CASE WHEN DeliveryCD <> @iDeliveryCD THEN @iDeliveryCD ELSE DeliveryCD END 
				  FROM tbGtcOrdEnr  WHERE OrdEnrNo = @iOrdEnrNo
			END
		

		-- 배송비용 계산
		
		IF @wItemGrpCd ='SP'
			BEGIN
				--20230201부터 변경단가 적용
				IF (CONVERT(VARCHAR, GETDATE(),112) >= '20230201')
				BEGIN
					/* 현불화물일때는 (L:3000,M:2000,S:2000) */				
					IF @w_DeliveryCD = '02'
					BEGIN
						-- 20230125 L,M,S 단가 변경(L : 3000 -> 3200, M,S : -> 2000 -> 2300)
						SELECT @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 3200
								, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 2300
								, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 2300
								, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 3200
								, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 2300
								, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 2300
						--SELECT @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 3000
						--		, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 2000
						--		, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 2000
						--		, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 3000
						--		, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 2000
						--		, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 2000
					END

					IF @w_DeliveryCD IN ('01') --01:현불택배
					-- 20210119 S 단가 변경 (4000->3500)
					-- 20211201 M,L 화물비 변경 
					-- 20230125 M 단가 변경(4100 -> 4600)
					BEGIN
						SELECT @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 5000
								--, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 4000
								, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 4600
								--, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 4000
								--, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 3500
								, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 3650

								, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 5000
								--, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 4000
								, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 4600
								--, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 4000
								--, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 3500
								, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 3650
					END
					IF @w_DeliveryCD IN ('03') --03:현불화물출고료부담
					BEGIN
						-- 20230125 L,M,S 단가 변경(L : 3000 -> 3200, M,S : -> 2000 -> 2300)
						-- 20230125 박스당 단가 변경 (1000 -> 1100)
						SELECT    @w_L_BoxFee = (ISNULL(@iL_BoxQty,0) * 3200) + (ISNULL(@iL_BoxQty,0) * 1100)
								, @w_M_BoxFee = (ISNULL(@iM_BoxQty,0) * 2300) + (ISNULL(@iM_BoxQty,0) * 1100)
								, @w_S_BoxFee = (ISNULL(@iS_BoxQty,0) * 2300) + (ISNULL(@iS_BoxQty,0) * 1100)
								, @w_L_BskFee = (ISNULL(@iL_BskQty,0) * 3200) + (ISNULL(@iL_BskQty,0) * 1100)
								, @w_M_BskFee = (ISNULL(@iM_BskQty,0) * 2300) + (ISNULL(@iM_BskQty,0) * 1100)
								, @w_S_BskFee = (ISNULL(@iS_BskQty,0) * 2300) + (ISNULL(@iS_BskQty,0) * 1100)

						--SELECT    @w_L_BoxFee = (ISNULL(@iL_BoxQty,0) * 3000) + (ISNULL(@iL_BoxQty,0) * 1000)
						--		, @w_M_BoxFee = (ISNULL(@iM_BoxQty,0) * 2000) + (ISNULL(@iM_BoxQty,0) * 1000)
						--		, @w_S_BoxFee = (ISNULL(@iS_BoxQty,0) * 2000) + (ISNULL(@iS_BoxQty,0) * 1000)
						--		, @w_L_BskFee = (ISNULL(@iL_BskQty,0) * 3000) + (ISNULL(@iL_BskQty,0) * 1000)
						--		, @w_M_BskFee = (ISNULL(@iM_BskQty,0) * 2000) + (ISNULL(@iM_BskQty,0) * 1000)
						--		, @w_S_BskFee = (ISNULL(@iS_BskQty,0) * 2000) + (ISNULL(@iS_BskQty,0) * 1000)
					END			
				END
				ELSE
				BEGIN
					/* 현불화물일때는 (L:3000,M:2000,S:2000) */
					IF @w_DeliveryCD = '02'
					BEGIN
						SELECT @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 3000
								, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 2000
								, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 2000
								, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 3000
								, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 2000
								, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 2000
					END

					IF @w_DeliveryCD IN ('01') --01:현불택배
					-- 20210119 S 단가 변경 (4000->3500)
					-- 20211201 M,L 화물비 변경 
					BEGIN
						SELECT @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 5000
								--, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 4000
								, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 4100
								--, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 4000
								--, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 3500
								, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 3650

								, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 5000
								--, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 4000
								, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 4100
								--, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 4000
								--, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 3500
								, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 3650
					END
					IF @w_DeliveryCD IN ('03') --03:현불화물출고료부담
					BEGIN
						SELECT    @w_L_BoxFee = (ISNULL(@iL_BoxQty,0) * 3000) + (ISNULL(@iL_BoxQty,0) * 1000)
								, @w_M_BoxFee = (ISNULL(@iM_BoxQty,0) * 2000) + (ISNULL(@iM_BoxQty,0) * 1000)
								, @w_S_BoxFee = (ISNULL(@iS_BoxQty,0) * 2000) + (ISNULL(@iS_BoxQty,0) * 1000)
								, @w_L_BskFee = (ISNULL(@iL_BskQty,0) * 3000) + (ISNULL(@iL_BskQty,0) * 1000)
								, @w_M_BskFee = (ISNULL(@iM_BskQty,0) * 2000) + (ISNULL(@iM_BskQty,0) * 1000)
								, @w_S_BskFee = (ISNULL(@iS_BskQty,0) * 2000) + (ISNULL(@iS_BskQty,0) * 1000)
					END							
				END

				SET @w_TransitFee = @w_L_BoxFee + @w_M_BoxFee + @w_S_BoxFee + @w_L_BskFee + @w_M_BskFee + @w_S_BskFee 
				SET @w_TransitVat = ROUND(@w_TransitFee * 0.1,0)
				SET @w_TransitSum = @w_TransitFee + @w_TransitVat

				--주문금액(공급가액)이 5백만원 이상 OR 미납배송 OR 착불화물이면 무료
				IF  @w_DeliveryCD IN ('04','05','06','99')
				BEGIN
						SELECT @w_L_BoxFee = 0
							, @w_M_BoxFee = 0
							, @w_S_BoxFee = 0
							, @w_L_BskFee = 0
							, @w_M_BskFee = 0
							, @w_S_BskFee = 0
							, @w_TransitFee =0
							, @w_TransitVat =0
							, @w_TransitSum =0
				END
			END	
		IF @wItemGrpCd ='GP'
			BEGIN
			
				/* 현불화물일때는 (L:3000,M:2000,S:2000) */
				IF @w_DeliveryCD = '02'
				BEGIN
							SELECT @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 5000
							, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 4000
							, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 3000
							, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 3000
							, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 2000
							, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 2000
					--SELECT @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 3000
					--		, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 2000
					--		, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 2000
					--		, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 3000
					--		, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 2000
					--		, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 2000
				END

				IF @w_DeliveryCD IN ('01') --01:현불택배
				-- 20211201 S,M 화물비변경 (S: 3500 -> 3650, M: 4000 -> 4100)
				-- 20221013 S,M,L 박스 화물비 0원으로 변경처리
				-- 20221014 S,M,L 박스 화물비 6000원으로 변경처리 및 바스켓 금액 원복
				BEGIN
					SELECT    @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 7000
							-- @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 6000
							-- @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 0		--20221013요청
							-- @w_L_BoxFee = ISNULL(@iL_BoxQty,0) * 5000	--20221013요청이전

							, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 6000
							--, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 6000		--20221014요청
							--, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 0		--20221013 요청
							--, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 4100	--20211201 이후
							--, @w_M_BoxFee = ISNULL(@iM_BoxQty,0) * 4000	--20211130 까지
							
							, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 5000
							--, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 6000		--20221014요청
							--, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 0		--20221013 요청
							--, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 3650	--20211201 이후
							--, @w_S_BoxFee = ISNULL(@iS_BoxQty,0) * 3500	--20211130 까지

							, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 5000		
							--, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 0		--20221013요청
							--, @w_L_BskFee = ISNULL(@iL_BskQty,0) * 5000	--20221013요청이전

							, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 4100		--20221014요청
							--, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 0		--20221013 요청
							--, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 4100	--20211201 이후
							--, @w_M_BskFee = ISNULL(@iM_BskQty,0) * 4000	--20211130 까지

							, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 3650		--20221014요청
							--, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 0		--20221013 요청
							--, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 3650	--20211201 이후
							--, @w_S_BskFee = ISNULL(@iS_BskQty,0) * 3500	--20211130 까지
				END
				IF @w_DeliveryCD IN ('03') --03:현불화물출고료부담
				BEGIN
					SELECT   @w_L_BoxFee = (ISNULL(@iL_BoxQty,0) * 6500) --+ (ISNULL(@iL_BoxQty,0) * 1000)
							, @w_M_BoxFee = (ISNULL(@iM_BoxQty,0) * 5200) --+ (ISNULL(@iM_BoxQty,0) * 1000)
							, @w_S_BoxFee = (ISNULL(@iS_BoxQty,0) * 3900) --+ (ISNULL(@iS_BoxQty,0) * 1000)
							--@w_L_BoxFee = (ISNULL(@iL_BoxQty,0) * 3000) + (ISNULL(@iL_BoxQty,0) * 1000)
							--, @w_M_BoxFee = (ISNULL(@iM_BoxQty,0) * 2000) + (ISNULL(@iM_BoxQty,0) * 1000)
							--, @w_S_BoxFee = (ISNULL(@iS_BoxQty,0) * 2000) + (ISNULL(@iS_BoxQty,0) * 1000)
							, @w_L_BskFee = (ISNULL(@iL_BskQty,0) * 3000) + (ISNULL(@iL_BskQty,0) * 1000)
							, @w_M_BskFee = (ISNULL(@iM_BskQty,0) * 2000) + (ISNULL(@iM_BskQty,0) * 1000)
							, @w_S_BskFee = (ISNULL(@iS_BskQty,0) * 2000) + (ISNULL(@iS_BskQty,0) * 1000)
				END
			
				SET @w_TransitFee = @w_L_BoxFee + @w_M_BoxFee + @w_S_BoxFee + @w_L_BskFee + @w_M_BskFee + @w_S_BskFee 
				SET @w_TransitVat = ROUND(@w_TransitFee * 0.1,0)
				SET @w_TransitSum = @w_TransitFee + @w_TransitVat

				--주문금액(공급가액)이 5백만원 이상 OR 미납배송 OR 착불화물이면 무료
				IF  @w_DeliveryCD IN ('04','05','06','99')
				BEGIN
						SELECT @w_L_BoxFee = 0
							, @w_M_BoxFee = 0
							, @w_S_BoxFee = 0
							, @w_L_BskFee = 0
							, @w_M_BskFee = 0
							, @w_S_BskFee = 0
							, @w_TransitFee =0
							, @w_TransitVat =0
							, @w_TransitSum =0
				END
			END

		IF @ipkgEnrNo <= 0 
			BEGIN

				IF EXISTS(	SELECT *
							  FROM MISTW..tbGtcPkgEnr x
							 WHERE x.OrdEnrNo = @iOrdEnrNo)	
					BEGIN
						SET @w_ErrYN = 'Y'
						SET @w_ErrMsg = '다른곳에서 이미 패킹정보를 저장하였습니다. 재조회 후 다시 입력하시기 바랍니다!'
						GOTO ENDSTEP
					END


				EXEC proc_GetcomNo_S 'TK', @wGetDate, @wpkgNo OUTPUT
				SET @wpkgEnrNo = NEXT VALUE FOR seq_GtcSequence

				INSERT INTO tbGtcPkgEnr (
							 pkgEnrNo            -- 패킹등록번호
							,OrdEnrNo            -- 주문등록번호
							,pkgNo               -- 패킹번호
							,pkgYmd              -- 패킹일자
							,AppointEmpNo
							,pkgWorkRmk          -- 패킹작업비고
							,CustCd              -- 거래처코드
							,EquipCustNm         -- 설비업체
							,Site                -- 현장
							,DeliveryPlace       -- 화물지점
							,TelNo               -- 전화번호
							,Adr                 -- 주소
							,L_BoxQty            -- L_BOX수량
							,M_BoxQty            -- M_BOX수량
							,S_BoxQty            -- S_BOX수량
							,L_BoxFee            -- L_BOX비용
							,M_BoxFee            -- M_BOX비용
							,S_BoxFee            -- S_BOX비용
							,L_BskQty            -- L_BSK수량
							,M_BskQty            -- M_BSK수량
							,S_BskQty            -- S_BSK수량
							,L_BskFee            -- L_BSK비용
							,M_BskFee            -- M_BSK비용
							,S_BskFee            -- S_BSK비용
							,PaletQty            -- 파레트수량
							,PaletFee            -- 퍄레트비용
							,TransitFee          -- 택배료
							,TransitVat          -- 택배로부가세
							,TransitSum          -- 배송료합계
							,pkgOrdRmk           -- 패킹지시비고
							,DeliveryNo          -- 출고번호
							,LineColor           -- 라인색상
							,LineMemo            -- 라인메모
							,MemoID              -- 메모관리번호
							,ColorID             -- 색상관리번호
							,PgNo                -- 프로그램번호
							,AddEmpNo            -- 등록자
							,AddDt               -- 등록일
							,UpdEmpNo            -- 수정자
							,UpdDt               -- 수정일
							,PkgStat
							,ItemGrpCd           --품목군
				) 
				VALUES(		 @wpkgEnrNo            -- 패킹등록번호
							,@iOrdEnrNo            -- 주문등록번호
							,@wpkgNo               -- 패킹번호
							,@ipkgYmd              -- 패킹일자
							,@iAppointEmpNo
							,@ipkgWorkRmk          -- 패킹작업비고
							,@iCustCd              -- 거래처코드
							,@iEquipCustNm         -- 설비업체
							,@iSite                -- 현장
							,@iDeliveryPlace       -- 화물지점
							,@iTelNo               -- 전화번호
							,@iAdr                 -- 주소
							,@iL_BoxQty            -- L_BOX수량
							,@iM_BoxQty            -- M_BOX수량
							,@iS_BoxQty            -- S_BOX수량
							,@w_L_BoxFee           -- L_BOX비용
							,@w_M_BoxFee           -- M_BOX비용
							,@w_S_BoxFee           -- S_BOX비용
							,@iL_BskQty            -- L_BSK수량
							,@iM_BskQty            -- M_BSK수량
							,@iS_BskQty            -- S_BSK수량
							,@w_L_BskFee           -- L_BSK비용
							,@w_M_BskFee           -- M_BSK비용
							,@w_S_BskFee           -- S_BSK비용
							,@iPaletQty            -- 파레트수량
							,0                     -- 퍄레트비용
							,@w_TransitFee         -- 택배료
							,@w_TransitVat         --비용부가세          -- 택배로부가세
							,@w_TransitSum         --비용계
							,@ipkgOrdRmk           -- 패킹지시비고
							,@iDeliveryNo          -- 출고번호
							,''						-- 라인색상
							,''						-- 라인메모
							,null					-- 메모관리번호
							,null					-- 색상관리번호
							,@iPgNo					-- 프로그램번호
							,@iAddEmpNo				-- 등록자
							,GETDATE()               -- 등록일
							,@iAddEmpNo				-- 수정자
							,GETDATE()               -- 수정일
							,@iPkgStat					--데이터 입력되면 패킹중(02)
							,@wItemGrpCd             --품목군
							)
			END
		ELSE 
			BEGIN
				UPDATE a
				   SET OrdEnrNo     = @iOrdEnrNo        -- 주문등록번호 
					,  pkgYmd       = @ipkgYmd          -- 패킹일자
					,  AppointEmpNo = @iAppointEmpNo	--지정작업자
					,  PkgStat      = @iPkgStat			--패킹상태
					,  pkgWorkRmk   = @ipkgWorkRmk      -- 패킹작업비고 
					,  CustCd       = @iCustCd          -- 거래처코드 
					,  EquipCustNm  = @iEquipCustNm     -- 설비업체 
					,  Site         = @iSite            -- 현장 
					,  DeliveryPlace = @iDeliveryPlace  -- 화물지점 
					,  TelNo         = @iTelNo          -- 전화번호 
					,  Adr           = @iAdr            -- 주소 
					,  L_BoxQty = @iL_BoxQty            -- L_BOX수량 
					,  M_BoxQty = @iM_BoxQty            -- M_BOX수량 
					,  S_BoxQty = @iS_BoxQty            -- S_BOX수량 
					,  L_BoxFee = @w_L_BoxFee            -- L_BOX비용 
					,  M_BoxFee = @w_M_BoxFee            -- M_BOX비용 
					,  S_BoxFee = @w_S_BoxFee            -- S_BOX비용 
					,  L_BskQty = @iL_BskQty            -- L_BSK수량 
					,  M_BskQty = @iM_BskQty            -- M_BSK수량 
					,  S_BskQty = @iS_BskQty            -- S_BSK수량 
					,  L_BskFee = @w_L_BskFee            -- L_BSK비용 
					,  M_BskFee = @w_M_BskFee           -- M_BSK비용 
					,  S_BskFee = @w_S_BskFee            -- S_BSK비용 
					,  PaletQty = @iPaletQty          -- 파레트수량
					,  PaletFee = @iPaletFee            -- 퍄레트비용 
					,  TransitFee = @w_TransitFee          -- 택배료 
					,  TransitVat = @w_TransitVat          -- 택배로부가세 
					,  TransitSum = @w_TransitSum          -- 배송료합계 
					,  pkgOrdRmk = @ipkgOrdRmk           -- 패킹지시비고 
					,  DeliveryNo = @iDeliveryNo          -- 출고번호  
					,  PgNo = @iPgNo                -- 프로그램번호 
					,  UpdEmpNo = @iAddEmpNo            -- 수정자 
					,  UpdDt = GETDATE()               -- 수정일				
				  FROM tbGtcPkgEnr AS a
				 WHERE pkgEnrNo = @ipkgEnrNo
			END	

		/*주문번호에 해상하는 배송방법 갱신*/
		UPDATE A
		SET DeliveryCD = @iDeliveryCD
		FROM MISTW..tbGtcOrdEnr  A
		WHERE OrdEnrNo =  @iOrdEnrNo 
             
		/*출고정보에 배송방법 변경*/
		UPDATE A 
		SET DeliveryCD = @iDeliveryCD
		FROM  MISTO..TODeliveryMaster A
		WHERE DeliveryNo = @iDeliveryNo
             
		/*출고정보에 배송방법 변경*/
		UPDATE A 
		SET DeliveryCD = @iDeliveryCD
		FROM  MISTO..TOContractMaster A
		WHERE ContractNo = @iContractNo

	END
END

/****************/
ENDSTEP:
/****************/

	SELECT	@w_ErrYN ERRYN, @w_ErrMsg ERRMSG, @wpkgNo PKGNO, @wpkgEnrNo PKGENRNO

RETURN
```

<a id="mistw-proc-gtci005-s-00"></a>
## `MISTW.dbo.proc_GTCI005_S_00`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_GTCI005_S_00.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_GTCI005_S_00.sql

```sql


					
/********************************************************************************
제    목 : 매출마감확인 조회
프로그램 : 매출마감확인
등 록 일 : 2019-06-21
등 록 자 : 양장미
수정일		수정자		내용
-----------------------------------------------------------------------
EXEC MISTW.dbo.proc_GTCI005_S_00 @iGtcCloseYm = '201906', @iCustCd = '', @iSalesGb = '%', @iItemGrpCd = 'SP'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_GTCI005_S_00]
		@iGtcCloseYm		VARCHAR(6)
	  , @iCustCd			VARCHAR(50)
	  , @iSalesGb			VARCHAR(10)
	  , @iItemGrpCd			VARCHAR(10)
AS


DECLARE @wFrYmd			VARCHAR(8) = @iGtcCloseYm + '01'
	  , @wToYmd			VARCHAR(8) = @iGtcCloseYm + '31'
	  , @wFrYmd2		VARCHAR(8) = CONVERT(varchar(6), DATEADD(M, -1, @iGtcCloseYm + '01'), 112) + '26'
	  , @wToYmd2		VARCHAR(8) = @iGtcCloseYm + '25'

DECLARE @tmp TABLE (
		CustCd			VARCHAR(10)
	  , ItemGrpCd		VARCHAR(2)
	  , SalesGb			VARCHAR(2)	
	  , SalesGb_Etc		VARCHAR(5)
	  , CalYm			VARCHAR(6)
	  , SupplyAmt		MONEY
)
	
   
BEGIN

	SET NOCOUNT ON;


	-- 판매
	IF @iSalesGb = '%' OR @iSalesGb = '04'
	BEGIN
		INSERT INTO @tmp
		SELECT a.CustCd
			 , a.ItemGrpCd
			 , '04'
			 , ''
			 , ''
			 , SUM(b.SupAmt)
		FROM   MISTO.dbo.TODeliveryMaster a
		INNER JOIN MISTO.dbo.TODeliveryDetail b ON b.DeliveryNo = a.DeliveryNo
		INNER JOIN MISTW.dbo.tbGtcOrdEnr c ON c.DeliveryNo = a.DeliveryNo
		INNER JOIN MISSA.dbo.SACust s ON s.CustCd = a.CustCd
		WHERE  a.DeliveryYmd BETWEEN CASE WHEN s.CloseApplydd = '25' THEN @wFrYmd2 ELSE @wFrYmd END AND CASE WHEN s.CloseApplydd = '25' THEN @wToYmd2 ELSE @wToYmd END
		AND    a.ItemGrpCd = CASE WHEN @iItemGrpCd = '' THEN a.ItemGrpCd ELSE @iItemGrpCd END
		AND    a.CustCd = CASE WHEN @iCustCd = '' THEN a.CustCd ELSE @iCustCd END
		AND    c.OrdGB IN ('SP02', 'GP02')
		GROUP BY a.CustCd, a.ItemGrpCd
	END

	-- 임대
	IF @iSalesGb = '%' OR @iSalesGb = '03'
	BEGIN
		INSERT INTO @tmp
		SELECT a.CustCd
			 , a.ItemGrpCd
			 , '03'
			 , ''
			 , ''
			 , SUM(CONVERT(money, a.RAMT))
		FROM   MISTO.dbo.TORentCalcHistory a
		INNER JOIN MISTO.dbo.TOCustDeadYmdMgm b ON b.CustCd = a.CUSTCD AND b.CalYm = a.STANDARDYM AND b.ItemGrpCd = a.ItemGrpCd
		--INNER JOIN MISSA.dbo.SACust s ON s.CustCd = a.CustCd
		WHERE  a.STANDARDYM = @iGtcCloseYm
		AND    b.ThisMonDeadYmd BETWEEN @wFrYmd AND @wToYmd
		AND    a.ItemGrpCd = CASE WHEN @iItemGrpCd = '' THEN a.ItemGrpCd ELSE @iItemGrpCd END
		AND    a.CustCd = CASE WHEN @iCustCd = '' THEN a.CustCd ELSE @iCustCd END
		AND    a.ItemGrpCd = 'SP'
		AND    CONVERT(money, a.RAMT) <> 0
		GROUP BY a.CustCd, a.ItemGrpCd

		INSERT INTO @tmp
		SELECT a.CustCd
			 , a.ItemGrpCd
			 , '03'
			 , ''
			 , ''
			 , SUM(CONVERT(money, a.RAMT))
		FROM   MISTO.dbo.TORentCalcHistoryGP a
		INNER JOIN MISTO.dbo.TOCustDeadYmdMgm b ON b.CustCd = a.CUSTCD AND b.CalYm = a.STANDARDYM AND b.ItemGrpCd = a.ItemGrpCd
		--INNER JOIN MISSA.dbo.SACust s ON s.CustCd = a.CustCd
		WHERE  a.STANDARDYM = @iGtcCloseYm
		AND    b.ThisMonDeadYmd BETWEEN @wFrYmd AND @wToYmd
		AND    a.ItemGrpCd = CASE WHEN @iItemGrpCd = '' THEN a.ItemGrpCd ELSE @iItemGrpCd END
		AND    a.CustCd = CASE WHEN @iCustCd = '' THEN a.CustCd ELSE @iCustCd END
		AND    a.ItemGrpCd = 'GP'
		AND    CONVERT(money, a.RAMT) <> 0
		GROUP BY a.CustCd, a.ItemGrpCd
	END

	-- 연체료
	IF @iSalesGb = '%' OR @iSalesGb = '05'
	BEGIN
		INSERT INTO @tmp
		SELECT a.CustCd
			 , a.ItemGrpCd
			 , '05'
			 , ''
			 , ''
			 , SUM(b.RealLateFee)
		FROM   MISTO.dbo.tbASLate a
		INNER JOIN MISTO.dbo.tbASLateDtl b ON b.AsLateMgmNo = a.AsLateMgmNo
		INNER JOIN MISSA.dbo.SACust s ON s.CustCd = a.CustCd
		WHERE  a.CalcYmd BETWEEN CASE WHEN s.CloseApplydd = '25' THEN @wFrYmd2 ELSE @wFrYmd END AND CASE WHEN s.CloseApplydd = '25' THEN @wToYmd2 ELSE @wToYmd END
		AND    a.ItemGrpCd = CASE WHEN @iItemGrpCd = '' THEN a.ItemGrpCd ELSE @iItemGrpCd END
		AND    a.CustCd = CASE WHEN @iCustCd = '' THEN a.CustCd ELSE @iCustCd END
		GROUP BY a.CustCd, a.ItemGrpCd
	END

	-- AS청구비
	IF @iSalesGb = '%' OR @iSalesGb = '06'
	BEGIN
		INSERT INTO @tmp
		SELECT c.CustCd
			 , b.ItemGrpCd
			 , '06'
			 , ''
			 , ''
			 , SUM(d.SupAmt)
		FROM   MISTO.dbo.TOAsFault a
		INNER JOIN MISTO.dbo.TOAs b ON b.AsNo = a.AsNo
		INNER JOIN MISTO.dbo.TODeliveryMaster c ON c.DeliveryNo = b.DeliveryNo
		INNER JOIN MISTO.dbo.TOAsPart d ON d.AsNo = a.AsNo AND d.Seq = a.Seq
		INNER JOIN MISSA.dbo.SACust s ON s.CustCd = c.CustCd
		WHERE  a.SubmitYmd BETWEEN CASE WHEN s.CloseApplydd = '25' THEN @wFrYmd2 ELSE @wFrYmd END AND CASE WHEN s.CloseApplydd = '25' THEN @wToYmd2 ELSE @wToYmd END
		AND    a.SubmitYn = 'Y'
		AND    b.ItemGrpCd = CASE WHEN @iItemGrpCd = '' THEN a.ItemGrpCd ELSE @iItemGrpCd END
		AND    c.CustCd = CASE WHEN @iCustCd = '' THEN c.CustCd ELSE @iCustCd END
		GROUP BY c.CustCd, b.ItemGrpCd
	END

	-- 공구분실
	IF @iSalesGb = '%' OR @iSalesGb = '07'
	BEGIN
		INSERT INTO @tmp
		SELECT c.CustCd
			 , a.ItemGrpCd
			 , '07'
			 , ''
			 , ''
			 , SUM(a.Amt)
		FROM   MISTO.dbo.ToLoss a
		INNER JOIN MISTO.dbo.TODeliveryMaster c ON c.DeliveryNo = a.DeliveryNo
		INNER JOIN MISSA.dbo.SACust s ON s.CustCd = c.CustCd
		WHERE  a.LossYmd BETWEEN CASE WHEN s.CloseApplydd = '25' THEN @wFrYmd2 ELSE @wFrYmd END AND CASE WHEN s.CloseApplydd = '25' THEN @wToYmd2 ELSE @wToYmd END
		AND    a.ItemGrpCd = CASE WHEN @iItemGrpCd = '' THEN a.ItemGrpCd ELSE @iItemGrpCd END
		AND    c.CustCd = CASE WHEN @iCustCd = '' THEN c.CustCd ELSE @iCustCd END
		GROUP BY c.CustCd, a.ItemGrpCd
	END

	-- 공구분실취소
	IF @iSalesGb = '%' OR @iSalesGb = '10'
	BEGIN
		INSERT INTO @tmp
		SELECT c.CustCd
			 , a.ItemGrpCd
			 , '10'
			 , ''
			 , ''
			 , -SUM(a.Amt)
		FROM   MISTO.dbo.ToLoss a
		INNER JOIN MISTO.dbo.TODeliveryMaster c ON c.DeliveryNo = a.DeliveryNo
		INNER JOIN MISSA.dbo.SACust s ON s.CustCd = c.CustCd
		WHERE  a.CancelYmd BETWEEN CASE WHEN s.CloseApplydd = '25' THEN @wFrYmd2 ELSE @wFrYmd END AND CASE WHEN s.CloseApplydd = '25' THEN @wToYmd2 ELSE @wToYmd END
		AND    a.ItemGrpCd = CASE WHEN @iItemGrpCd = '' THEN a.ItemGrpCd ELSE @iItemGrpCd END
		AND    c.CustCd = CASE WHEN @iCustCd = '' THEN c.CustCd ELSE @iCustCd END
		GROUP BY c.CustCd, a.ItemGrpCd
	END

	-- 기타청구
	--IF @iSalesGb = '%' OR @iSalesGb = '09'
	--BEGIN
		INSERT INTO @tmp
		SELECT a.CustCd
			 , a.ItemGrpCd
			 , a.SalesGb
			 , a.SalesGb + '_09'
			 -- 20220126 / 매출집계 생성 시 출고년월이 다른 경우 별개의 레코드로 자료가 생성되어 비교대상을 분리할 수 있도록 추가함
			 , LEFT(a.DeliveryYmd,6)
			 , SUM(b.SupplyAmt)
		FROM   MISTO.dbo.tbGtcTrade a
		INNER JOIN MISTO.dbo.tbGtcTradeDetail b ON b.TradeMgmNo = a.TradeMgmNo
		INNER JOIN MISSA.dbo.SACust s ON s.CustCd = a.CustCd
		WHERE  a.DeliveryYmd BETWEEN CASE WHEN s.CloseApplydd = '25' THEN @wFrYmd2 ELSE @wFrYmd END AND CASE WHEN s.CloseApplydd = '25' THEN @wToYmd2 ELSE @wToYmd END
		AND    a.ItemGrpCd = CASE WHEN @iItemGrpCd = '' THEN a.ItemGrpCd ELSE @iItemGrpCd END
		AND    a.CustCd = CASE WHEN @iCustCd = '' THEN a.CustCd ELSE @iCustCd END
		AND    a.SalesGb = CASE WHEN @iSalesGb = '%' THEN a.SalesGb ELSE @iSalesGb END
		GROUP BY a.CustCd, a.ItemGrpCd, a.SalesGb, LEFT(a.DeliveryYmd,6)
	--END

	-- 공구화물비
	IF @iSalesGb = '%' OR @iSalesGb = '13'
	BEGIN
		INSERT INTO @tmp
		SELECT a.CustCd
			 , a.ItemGrpCd
			 , '13'
			 , ''
			 , ''
			 , SUM(b.TransitFee)
		FROM   MISTW.dbo.tbGtcOrdEnr a
		INNER JOIN MISTW.dbo.tbGtcPkgEnr b ON b.OrdEnrNo = a.OrdEnrNo
		INNER JOIN MISSA.dbo.SACust s ON s.CustCd = a.CustCd
		CROSS APPLY MISTW.dbo.fn_ordDeliveryYmd(a.OrdEnrNo, RIGHT(a.OrdGB, 2)) c
		WHERE  c.DeliveryYmd BETWEEN CASE WHEN s.CloseApplydd = '25' THEN @wFrYmd2 ELSE @wFrYmd END AND CASE WHEN s.CloseApplydd = '25' THEN @wToYmd2 ELSE @wToYmd END
		AND    a.OutStat = '04'
		AND    a.ItemGrpCd = CASE WHEN @iItemGrpCd = '' THEN a.ItemGrpCd ELSE @iItemGrpCd END
		AND    a.CustCd = CASE WHEN @iCustCd = '' THEN a.CustCd ELSE @iCustCd END
		AND    ISNULL(b.TransitFee, 0) <> 0
		GROUP BY a.CustCd, a.ItemGrpCd
	END


	SELECT DBO.fn_comCustNm(ISNULL(a.CustCd, b.CustCd))    AS CUSTNM			--대리점
	     , DBO.fn_comCodeNm('LOG014',ISNULL(a.SalesGb, b.SalesGb),'1' )       AS SALESGBNM			--매출구분 
		 , ISNULL(a.OrgSupplyAmt,0)                        AS ORGSUPPLYAMT		--임대료
		 , ISNULL(a.RentSupAmt,0)                          AS RENTSUPAMT		--지원금액
		 , ISNULL(a.SupplyAmt,0) + ISNULL(a.TransitFee,0)  AS SUPPLYAMT			--공급가액
		 , ISNULL(a.Vat,0)       + ISNULL(a.TransitVat,0)  AS VAT				--부가세
		 , ISNULL(a.Amt,0)       + ISNULL(a.TransitSum,0)  AS AMT				--금액
		 , a.ToolCustTotalNo                               AS TOOLCUSTTOTALNO	--공구거래처별집계번호(H)
		 , MISCM.DBO.fn_gfnDateTypeTrans(a.GtcCloseYmd,'-')   AS GTCCLOSEYMD    --공구마감일자
		 , ISNULL(b.SupplyAmt, 0)						   AS MONTHLYAMT		--판매월보금액
		 , ISNULL(a.CustCd, b.CustCd)					   AS CUSTCD			--거래처코드
         , ISNULL(a.SalesGb, b.SalesGb)                    AS SALESGB			--매출구분(H)
         , ISNULL(a.SalesGb_Etc, ISNULL(b.SalesGb_Etc, ''))   AS SALESGBETC		--매출구분(H)
		 , ISNULL(a.TotalYm, @iGtcCloseYm)                    AS TOTALYM		--집계년월(H)
	FROM  (SELECT x.*, y.CalYm FROM MISTO..tbGtcCustTotal x
		   LEFT JOIN (SELECT ToolCustTotalNo, MAX(CalYm) CalYm FROM MISTO.dbo.tbGtcCustTotalDtl y GROUP BY ToolCustTotalNo) y ON y.ToolCustTotalNo = x.ToolCustTotalNo
		   WHERE  x.TotalYm     = @iGtcCloseYm
		   AND  ( @iCustCd    is null or @iCustCd    = ''  or (@iCustCd    is not null and @iCustCd    <> ''  and x.CustCd    = @iCustCd) )
		   AND  ( @iSalesGb   is null or @iSalesGb   = '%' or (@iSalesGb   is not null and @iSalesGb   <> '%' and x.SalesGb   = @iSalesGb) )
		   AND  ( @iItemGrpCd is null or @iItemGrpCd = ''  or (@iItemGrpCd is not null and @iItemGrpCd <> ''  and x.ItemGrpCd = @iItemGrpCd) )) a 
	FULL OUTER JOIN @tmp b ON b.CustCd = a.CustCd AND b.ItemGrpCd = a.ItemGrpCd AND b.SalesGb = a.SalesGb AND b.SalesGb_Etc = ISNULL(a.SalesGb_Etc, '') AND a.CalYm = CASE WHEN b.CalYm = '' THEN a.CalYm ELSe b.CalYm END
	ORDER BY 2, 1



END
```

<a id="mistw-proc-purc001-iu-01"></a>
## `MISTW.dbo.proc_PURC001_IU_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURC001_IU_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURC001_IU_01.sql

```sql



/********************************************************************************
제    목 : 발주등록 저장
프로그램 : 발주등록(원자재/외주공정)
등 록 일 : 2018-04-20
등 록 자 : 양장미
수정일		수정자		내용
-----------------------------------------------------------------------
EXEC MISTW.dbo.proc_PURC001_IU_01 0, '20180522', 'AB056', '01', '03', '000000881', 'ERP18'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_PURC001_IU_01]
		@iOrdMgmNo			int				-- 발주등록관리번호
	  , @iOrdYmd			varchar(8)		-- 발주일자
	  , @iCustCd			varchar(5)		-- 거래처코드
	  , @iTaxGb				varchar(15)		-- 과세구분
	  , @iBuyGb				varchar(15) = ''		-- 발주구분
	  , @iOrdType			varchar(15)		-- 발주유형
	  , @iPurRmk			varchar(1000)		-- 발주비고
	  , @iPgNo				varchar(20)		-- 프로그램번호
	  , @iAddEmpNo			varchar(10)		-- 등록자
	  , @iItemGrpCd			varchar(10)		-- 품목군
AS

DECLARE @wErrYN		VARCHAR(1),
		@wErrMsg	VARCHAR(1000)

DECLARE @wSysDate	VARCHAR(8) = CONVERT(varchar(8), GETDATE(), 112),
		@wOrdNo		VARCHAR(13) = ''

BEGIN

	SET NOCOUNT ON;

	SET @wErrYN = 'N'
	SET @wErrMsg = '정상 처리되었습니다.'


	BEGIN TRAN

	IF NOT EXISTS(SELECT * FROM MISSA.dbo.SACust WHERE CustCd = @iCustCd)
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = '거래처를 확인하세요'
		GOTO ENDSTEP
	END

	IF NOT EXISTS(SELECT * FROM MISCM.dbo.CMETCCD WHERE ORGCD = 'DST' AND INSTCD = '%' AND SYSIDCD = 'LO' AND COMKDCD = 'LOG003' AND CDBS = @iTaxGb)
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = '과세구분을 확인하세요'
		GOTO ENDSTEP
	END

	IF NOT EXISTS(SELECT * FROM MISCM.dbo.CMETCCD WHERE ORGCD = 'DST' AND INSTCD = '%' AND SYSIDCD = 'PD' AND COMKDCD = 'PDA049' AND CDBS = @iItemGrpCd)
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = '품목분류를 확인하세요'
		GOTO ENDSTEP
	END

	IF NOT EXISTS(SELECT * FROM MISCM.dbo.CMETCCD WHERE ORGCD = 'DST' AND INSTCD = '%' AND SYSIDCD = 'LO' AND COMKDCD = 'LOG012' AND CDBS = @iOrdType)
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = '발주유형을 확인하세요'
		GOTO ENDSTEP
	END

	IF @iOrdType IN ('05', '06') AND @iBuyGb = '' SELECT @iBuyGb = '01'
	IF NOT EXISTS(SELECT * FROM MISCM.dbo.CMETCCD WHERE ORGCD = 'DST' AND INSTCD = '%' AND SYSIDCD = 'PU' AND COMKDCD = 'PUR005' AND CDBS = @iBuyGb)
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = '발주구분을 확인하세요'
		GOTO ENDSTEP
	END


	IF @iOrdMgmNo = 0
	BEGIN
		SELECT @iOrdMgmNo = NEXT VALUE FOR seq_PurSequence
		EXEC proc_GetcomNo_S '04', @wSysDate, @wOrdNo OUTPUT

		INSERT INTO MISTW.dbo.tbPurOrd ( 
			    ItemGrpCd           -- 품목구분
			  ,	OrdMgmNo            -- 발주등록관리번호
			  , OrdNo               -- 발주번호
			  , OrdYmd              -- 발주일자
			  , CustCd              -- 거래처코드
			  , BuyGb               -- 발주구분
			  , TaxGb               -- 과세구분
			  , OrdType             -- 발주유형
			  , OrdYn               -- 발주여부
			  , OrdClosingYn        -- 발주종결여부
			  , PurRmk				-- 발주비고
			  , PgNo                -- 프로그램번호
			  , AddEmpNo            -- 등록자
			  , AddDt               -- 등록일
		)
		VALUES (
				@iItemGrpCd         -- 품목군
			  ,	@iOrdMgmNo          -- 발주등록관리번호
			  , @wOrdNo             -- 발주번호
			  , @iOrdYmd            -- 발주일자
			  , @iCustCd            -- 거래처코드
			  , @iBuyGb	            -- 발주구분
			  , @iTaxGb             -- 과세구분
			  , @iOrdType           -- 발주유형
			  , 'N'		            -- 발주여부
			  , 'N'				    -- 발주종결여부
			  , @iPurRmk			-- 발주비고
			  , @iPgNo              -- 프로그램번호
			  , @iAddEmpNo          -- 등록자
			  , GETDATE()           -- 등록일
		)

		IF @@ERROR <> 0 OR @@ROWCOUNT <= 0
		BEGIN
			SET @wErrYN = 'Y'
			SET @wErrMsg = 'tbPurOrd 테이블 저장중 오류'
			GOTO ENDSTEP
		END
	END
	ELSE
	BEGIN
		IF EXISTS(SELECT * FROM MISTW.dbo.tbPurOrd WHERE OrdMgmNo = @iOrdMgmNo AND (ISNULL(OrdYn, '') = 'Y' OR ISNULL(OrdClosingYn, '') = 'Y'))
		BEGIN
			SET @wErrYN = 'Y'
			SET @wErrMsg = '이미 발주되어 수정할 수 없습니다.'
			GOTO ENDSTEP
		END

		IF EXISTS(SELECT * FROM MISTW.dbo.tbPurOrdDtl WHERE OrdMgmNo = @iOrdMgmNo AND ISNULL(OrdClosingYn, '') = 'Y')
		BEGIN
			SET @wErrYN = 'Y'
			SET @wErrMsg = '발주 종결된 건이 있어 수정할 수 없습니다.'
			GOTO ENDSTEP
		END


		UPDATE MISTW.dbo.tbPurOrd
		SET    OrdYmd = @iOrdYmd
			 , CustCd = @iCustCd
			 , TaxGb = @iTaxGb
			 , BuyGb = @iBuyGb
			 , OrdType = @iOrdType
			 , PurRmk = @iPurRmk
			 , PgNo = @iPgNo
			 , UpdEmpNo = @iAddEmpNo
			 , UpdDt = GETDATE()
		WHERE  OrdMgmNo = @iOrdMgmNo

		IF @@ERROR <> 0 OR @@ROWCOUNT <= 0
		BEGIN
			SET @wErrYN = 'Y'
			SET @wErrMsg = 'tbPurOrd 테이블 저장중 오류'
			GOTO ENDSTEP
		END
	END


	GOTO ENDSTEP



/****************/
ENDSTEP:
/****************/
	IF @wErrYN = 'Y'
		ROLLBACK TRAN
	ELSE
		COMMIT TRAN

	SELECT @wErrYN ERRYN, @wErrMsg ERRMSG

END
```

<a id="mistw-proc-purc001-s-01"></a>
## `MISTW.dbo.proc_PURC001_S_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURC001_S_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURC001_S_01.sql

```sql
CREATE PROCEDURE [dbo].[proc_PURC001_S_01]
		@iOrdYmdF			varchar(8) = ''		-- 발주일자From
	  , @iOrdYmdT			varchar(8) = ''		-- 발주일자To
	  , @iCustCd			varchar(50) = ''	-- 거래처코드
	  , @iOrdYn				varchar(1) = ''		-- 발주여부
	  , @iItemGrpCd		    varchar(2)         -- 품목군
	  , @iDivision			varchar(10) = '%'	-- 구분(1:원자재,2:외주공정,3:소모품,4:상품 / '%':전체)
AS

BEGIN

	SET NOCOUNT ON


	SELECT 'N'					CHK				-- 선택
		 , a.OrdNo				ORDNO			-- 발주번호
		 , MISCM.dbo.fn_gfnDateTypeTrans(a.OrdYmd, '-')			 			ORDYMD		-- 발주일자
		 , a.CustCd 			CUSTCD			-- 거래처코드
		 , B.CustNm         	CUSTNM		    -- 거래처명
		 , a.TaxGb 				TAXGB			-- 과세구분
		 , A.BuyGb              BUYGB           -- 발주구분(01:구매,02:GP의뢰)
		 , a.OrdType 			ORDTYPE			-- 발주유형
		 , CASE WHEN a.OrdType IN ('01','03') THEN '1'
		        WHEN a.OrdType IN ('02','04') THEN '2'
		        WHEN a.OrdType = '05' THEN '3'
		        WHEN a.OrdType = '06' THEN '4'
		        ELSE '' END			DIVISION		-- 구분(파생)
		 , a.OrdYn 				ORDYN			-- 발주여부
		 , a.OrdMgmNo 			ORDMGMNO		-- 발주등록관리번호
		 , REPLACE(ISNULL(B.FaxNo,''),'-','')              FAXNO           -- 팩스번호
		 , a.PurRmk				PURRMK			-- 발주비고
		 , '' 					AUD
		 , ITEMGRPCD            ITEMGRPCD       -- 품목군
	FROM   MISTW.dbo.tbPurOrd a
	INNER JOIN MISSA.dbo.SACust B
	  ON A.CustCd = B.CustCd
	WHERE  a.OrdYmd BETWEEN @iOrdYmdF AND @iOrdYmdT
	AND    a.OrdYn LIKE @iOrdYn
	AND    a.OrdType IN (SELECT CDBS FROM MISCM.dbo.CMETCCD WHERE ORGCD = 'DST' AND INSTCD = '%' AND SYSIDCD = 'LO' AND COMKDCD = 'LOG012')
	AND  ( ISNULL(@iDivision,'%') IN ('','%')
	    OR ( @iDivision = '1' AND a.OrdType IN ('01','03') )
	    OR ( @iDivision = '2' AND a.OrdType IN ('02','04') )
	    OR ( @iDivision = '3' AND a.OrdType = '05' )
	    OR ( @iDivision = '4' AND a.OrdType = '06' ) )
	AND  ( ISNULL(@iCustCd, '') = '' OR (ISNULL(@iCustCd, '') <> '' AND a.CustCd = @iCustCd) )
	AND  ItemGrpCd = @iItemGrpCd
	ORDER BY a.OrdYmd, a.OrdMgmNo, a.OrdYn

END
```

<a id="mistw-proc-purc001-u-01"></a>
## `MISTW.dbo.proc_PURC001_U_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURC001_U_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURC001_U_01.sql

```sql



/********************************************************************************
제    목 : 발주 일자변경/확인/COPY
프로그램 : 발주등록(원자재/외주공정)
등 록 일 : 2018-04-16
등 록 자 : 양장미
수정일		수정자		내용
-----------------------------------------------------------------------
EXEC MISTW.dbo.proc_PURC001_U_01 'M', 1000771, '20180727', '000000881', 'ERP18'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_PURC001_U_01]
		@iWorkingTag		varchar(1)		
	  , @iOrdMgmNo			int				-- 발주등록관리번호
	  , @iOrdYmd			varchar(8)		-- 발주일자
	  , @iPgNo				varchar(20)		-- 프로그램번호
	  , @iAddEmpNo			varchar(10)		-- 등록자
AS

DECLARE @wErrYN		VARCHAR(1),
		@wErrMsg	VARCHAR(1000),
		@wErrNo		INT,
		@wRowCount	INT

DECLARE @wSysDate	VARCHAR(8) = CONVERT(varchar(8), GETDATE(), 112),
		@wOrdNo		VARCHAR(13) = '',
		@wOrdMgmNo	int

BEGIN

	SET NOCOUNT ON;

	SET @wErrYN = 'N'
	SET @wErrMsg = '정상 처리되었습니다.'

	IF @iWorkingTag = 'M'
		GOTO ORDYMD_UPDATE
	IF @iWorkingTag = 'Y'
		GOTO ORDYN_UPDATE
	IF @iWorkingTag = 'C'
		GOTO ORD_COPY


/****************/
ORDYMD_UPDATE:
/****************/

	IF EXISTS(SELECT * FROM MISTW.dbo.tbPurOrd 
			  WHERE  OrdMgmNo = @iOrdMgmNo AND (ISNULL(OrdYn, '') = 'Y' OR ISNULL(OrdClosingYn, '') = 'Y'))
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = '이미 발주되어 일자를 변경할 수 없습니다.'
		GOTO ENDSTEP
	END

	IF EXISTS(SELECT * FROM MISTW.dbo.tbPurOrdDtl WHERE OrdMgmNo = @iOrdMgmNo AND ISNULL(OrdClosingYn, '') = 'Y')
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = '발주 종결된 건이 있어 일자를 변경할 수 없습니다.'
		GOTO ENDSTEP
	END


	UPDATE MISTW.dbo.tbPurOrd
	SET    OrdYmd = @iOrdYmd
		 , PgNo = @iPgNo
		 , UpdEmpNo = @iAddEmpNo
		 , UpdDt = GETDATE()
	WHERE  OrdMgmNo = @iOrdMgmNo

	IF @@ERROR <> 0 OR @@ROWCOUNT <= 0
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = 'tbPurOrd 테이블 발주일자 저장중 오류'
		GOTO ENDSTEP
	END


	GOTO ENDSTEP


/****************/
ORDYN_UPDATE:
/****************/

	IF NOT EXISTS(SELECT * FROM MISTW.dbo.tbPurOrdDtl WHERE OrdMgmNo = @iOrdMgmNo)
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = '발주 상세내역이 입력되지 않았습니다.'
		GOTO ENDSTEP
	END


	UPDATE MISTW.dbo.tbPurOrd
	SET    OrdYn = 'Y'
		 , PgNo = @iPgNo
		 , UpdEmpNo = @iAddEmpNo
		 , UpdDt = GETDATE()
	WHERE  OrdMgmNo = @iOrdMgmNo

	IF @@ERROR <> 0 OR @@ROWCOUNT <= 0
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = 'tbPurOrd 테이블 발주여부 저장중 오류'
		GOTO ENDSTEP
	END


	GOTO ENDSTEP


/****************/
ORD_COPY:
/****************/

	SELECT @wOrdMgmNo = NEXT VALUE FOR seq_PurSequence
	EXEC proc_GetcomNo_S '04', @wSysDate, @wOrdNo OUTPUT

	INSERT INTO MISTW.dbo.tbPurOrd ( 
			OrdMgmNo            -- 발주등록관리번호
		  , OrdNo               -- 발주번호
		  , OrdYmd              -- 발주일자
		  , CustCd              -- 거래처코드
		  , BuyGb               -- 발주구분
		  , TaxGb               -- 과세구분
		  , OrdType             -- 발주유형
		  , PurRmk				-- 발주비고
		  , OrdYn               -- 발주여부
		  , OrdClosingYn        -- 발주종결여부
		  , PgNo                -- 프로그램번호
		  , AddEmpNo            -- 등록자
		  , AddDt               -- 등록일
		  , ItemGrpCd			-- 품목분류
	)
	SELECT @wOrdMgmNo
		 , @wOrdNo
		 , @iOrdYmd
		 , CustCd
		 , '01'					-- 발주구분(구매발주)
		 , TaxGb
		 , CASE OrdType WHEN '01' THEN '03' WHEN '02' THEN '04' ELSE OrdType END			-- 구매계획은 기타로 변경
		 , PurRmk
		 , 'N'
		 , 'N'
		 , @iPgNo
		 , @iAddEmpNo
		 , GETDATE()
		 , ItemGrpCd			-- 품목분류는 복사되는 자료와 동일하게(품목내역이 동일하므로)
	FROM   MISTW.dbo.tbPurOrd
	WHERE  OrdMgmNo = @iOrdMgmNo

	IF @@ERROR <> 0 OR @@ROWCOUNT <= 0
	BEGIN
		SET @wErrYN = 'Y'
		SET @wErrMsg = 'tbPurOrd 테이블 복사 저장중 오류'
		GOTO ENDSTEP
	END


	INSERT INTO MISTW.dbo.tbPurOrdDtl ( 
			OrdMgmDtlNo         -- 발주등록관리상세번호
		  , OrdMgmNo            -- 발주등록관리번호
		  , OrdReqMgmNo			-- 발주의뢰관리번호
		  , ItemNo              -- 품목코드
		  , ProcessCd           -- 공정코드
		  , DeliveryYmd         -- 납기일
		  , InExpectYmd         -- 입고예정일
		  , OrdQty              -- 발주수량
		  , UniPrice            -- 단가
		  , SupplyAmt           -- 공급가액
		  , Vat                 -- 부가세
		  , Amt                 -- 금액
		  , OrdClosingYn        -- 발주종결여부
		  , PgNo                -- 프로그램번호
		  , AddEmpNo            -- 등록자
		  , AddDt               -- 등록일
	)
	SELECT NEXT VALUE FOR seq_PurSequence
		 , @wOrdMgmNo
		 , 0
		 , ItemNo
		 , ProcessCd
		 , ''
		 , ''
		 , OrdQty
		 , UniPrice
		 , SupplyAmt
		 , Vat
		 , Amt
		 , 'N'
		 , @iPgNo
		 , @iAddEmpNo
		 , GETDATE()
	FROM   MISTW.dbo.tbPurOrdDtl
	WHERE  OrdMgmNo = @iOrdMgmNo

	SELECT @wErrNo = @@ERROR, @wRowCount = @@ROWCOUNT
	IF @wErrNo <> 0 OR @wRowCount <= 0
	BEGIN
		SET @wErrYN = 'Y'
		IF @wRowCount = 0
			SET @wErrMsg = '품목 내역이 없어 자료를 복사할 수 없습니다.'
		ELSE
			SET @wErrMsg = 'tbPurOrdDtl 테이블 복사 저장중 오류'
		GOTO ENDSTEP
	END


	GOTO ENDSTEP


/****************/
ENDSTEP:
/****************/

	SELECT	@wErrYN ERRYN, @wErrMsg ERRMSG

END
```

<a id="mistw-proc-purc003-s-01"></a>
## `MISTW.dbo.proc_PURC003_S_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURC003_S_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURC003_S_01.sql

```sql
-- ===================== DV-001M 발주입고 proc_PURC003_S_01 =====================
CREATE PROCEDURE [dbo].[proc_PURC003_S_01]
		@iInYm				varchar(6) = ''
	  , @iCustCd			varchar(50) = ''
	  , @iItemGrpCd			varchar(10) = ''
	  , @iDivision			varchar(10) = '%'	-- 구분(1원자재,2외주공정 / '%':전체)
AS
BEGIN
	SET NOCOUNT ON
	SELECT a.InNo INNO
		 , MISCM.dbo.fn_gfnDateTypeTrans(a.InYmd, '-') INYMD
		 , a.InGb INGB
		 , CASE WHEN a.InGb IN ('01','03') THEN '1'
		        WHEN a.InGb IN ('02','04') THEN '2'
		        WHEN a.InGb IN ('05','07') THEN '4'
		        WHEN a.InGb IN ('06','08') THEN '3' ELSE '' END DIVISION
		 , a.CustCd CUSTCD
		 , (SELECT CustNm FROM MISSA.dbo.SACust WHERE CustCd = a.CustCd) CUSTNM
		 , a.TaxGb TAXGB
		 , a.Remark REMARK
		 , a.InMgmNo INMGMNO
		 , '' AUD
		 , a.ItemGrpCd ITEMGRPCD
	FROM   MISTW.dbo.tbPurIn a
	WHERE  a.InYmd BETWEEN @iInYm + '01' AND @iInYm + '31'
	AND    a.InGb IN ('01', '02')
	AND  ( ISNULL(@iDivision,'%') IN ('','%')
	    OR ( @iDivision = '1' AND a.InGb IN ('01','03') )
	    OR ( @iDivision = '2' AND a.InGb IN ('02','04') )
	    OR ( @iDivision = '3' AND a.InGb IN ('06','08') )
	    OR ( @iDivision = '4' AND a.InGb IN ('05','07') ) )
	AND ( @iCustCd is null or @iCustCd = '' or ( @iCustCd is not null and @iCustCd <> '' and A.CustCd = @iCustCd ))
	AND  a.ItemGrpCd = @iItemGrpCd
	ORDER BY a.InYmd, a.CustCd, a.InMgmNo
END
```

<a id="mistw-proc-purc003-s-02"></a>
## `MISTW.dbo.proc_PURC003_S_02`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURC003_S_02.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURC003_S_02.sql

```sql


/********************************************************************************
제    목 : 입고상세내역 조회
프로그램 : 발주입고등록, 기타입고등록 - 원자재/외주공정,상품/소모품
등 록 일 : 2018-04-26
등 록 자 : 양장미
수정일		수정자		내용
-----------------------------------------------------------------------
EXEC proc_PURC003_S_02 4554927
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_PURC003_S_02]
		@iInMgmNo		int	= 0				-- 입고등록관리번호
AS

BEGIN

	SET NOCOUNT ON


	SELECT a.ItemNo					ITEMNO			-- 품목코드
		 , b.ItemNm					ITEMNM			-- 품목명
		 , b.Spec					SPEC			-- 규격
		 , a.ProcessCd				PROCESSCD		-- 공정코드
		 , c.ProcessNm				PROCESSNM		-- 공정명
		 --, a.GoodBadGb				GOODBADGB		-- 양품불량구분
		 , CASE WHEN a.GoodBadGb = '02' THEN '불량' ELSE '양품' END		GOODBADGB	-- 양품불량구분
		 , a.InQty					INQTY			-- 입고수량
		 , CASE WHEN b.Unit ='本' THEN 'M' ELSE B.Unit END			UNIT			-- 단위		 
		 , a.InPrice				INPRICE			-- 입고단가
		 , a.SupplyAmt				SUPPLYAMT		-- 공급가액
		 , a.Vat					VAT				-- 부가세
		 , a.Amt					AMT				-- 금액
		 , a.InRmk					INRMK			-- 비고
		 , a.InDtlMgmNo				INDTLMGMNO		-- 입고등록상세관리번호
		 , ''						AUD
	FROM   MISTW.dbo.tbPurInDtl a
	INNER JOIN MISPD.dbo.PDItemMaster b ON a.ItemNo = b.ItemNo
	LEFT JOIN MISPD.dbo.PDItemProcess c ON a.ItemNo = c.ItemNo AND a.ProcessCd = c.ProcessCd
	WHERE  a.InMgmNo = @iInMgmNo
	ORDER BY A.InDtlMgmNo



END
```

<a id="mistw-proc-purc009-s-01"></a>
## `MISTW.dbo.proc_PURC009_S_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURC009_S_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURC009_S_01.sql

```sql
-- ===================== DV-002M 기타입고 proc_PURC009_S_01 =====================
CREATE PROCEDURE [dbo].[proc_PURC009_S_01]
		@iInYm				varchar(6) = ''
	  , @iCustCd			varchar(50) = ''
	  , @iItemGrpCd			varchar(10) = ''
	  , @iDivision			varchar(10) = '%'	-- 구분(1원자재,2외주공정 / '%':전체)
AS
BEGIN
	SET NOCOUNT ON
	SELECT a.InNo INNO
		 , MISCM.dbo.fn_gfnDateTypeTrans(a.InYmd, '-') INYMD
		 , a.InGb INGB
		 , CASE WHEN a.InGb IN ('01','03') THEN '1'
		        WHEN a.InGb IN ('02','04') THEN '2'
		        WHEN a.InGb IN ('05','07') THEN '4'
		        WHEN a.InGb IN ('06','08') THEN '3' ELSE '' END DIVISION
		 , a.CustCd CUSTCD
		 , (SELECT CustNm FROM MISSA.dbo.SACust WHERE CustCd = a.CustCd) CUSTNM
		 , a.TaxGb TAXGB
		 , a.Remark REMARK
		 , a.InMgmNo INMGMNO
		 , '' AUD
		 , a.ItemGrpCd ITEMGRPCD
	FROM   MISTW.dbo.tbPurIn a
	WHERE  a.InYmd BETWEEN @iInYm + '01' AND @iInYm + '31'
	AND    a.InGb IN ('03', '04')
	AND  ( ISNULL(@iDivision,'%') IN ('','%')
	    OR ( @iDivision = '1' AND a.InGb IN ('01','03') )
	    OR ( @iDivision = '2' AND a.InGb IN ('02','04') )
	    OR ( @iDivision = '3' AND a.InGb IN ('06','08') )
	    OR ( @iDivision = '4' AND a.InGb IN ('05','07') ) )
	AND ( @iCustCd is null or @iCustCd = '' or ( @iCustCd is not null and @iCustCd <> '' and A.CustCd = @iCustCd ))
	AND ( @iItemGrpCd is null or @iItemGrpCd = '' or ( @iItemGrpCd is not null and @iItemGrpCd <> '' and A.ItemGrpCd = @iItemGrpCd ))
	ORDER BY a.InYmd, a.CustCd, a.InMgmNo
END
```

<a id="mistw-proc-purc010-s-01"></a>
## `MISTW.dbo.proc_PURC010_S_01`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURC010_S_01.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURC010_S_01.sql

```sql



/********************************************************************************
제    목 : 입고내역 조회
프로그램 : 기타입고등록(기타)
등 록 일 : 2018-04-27
등 록 자 : 양장미
수정일		수정자		내용
-----------------------------------------------------------------------
EXEC proc_PURC010_S_01 '201804', ''
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_PURC010_S_01]
		@iInYm				varchar(6) = ''		-- 입고년월
	  , @iCustCd			varchar(50) = ''	-- 거래처코드
	  , @iItemGrpCd			varchar(10) = ''	-- 품목분류
AS

BEGIN

	SET NOCOUNT ON


	SELECT a.InNo				INNO			-- 입고번호
		 , MISCM.dbo.fn_gfnDateTypeTrans(a.InYmd, '-')			 			INYMD		-- 입고일자
		 , a.InGb				INGB			-- 입고구분
		 , a.CustCd 			CUSTCD			-- 거래처코드
		 , (SELECT CustNm FROM MISSA.dbo.SACust WHERE CustCd = a.CustCd)	CUSTNM		-- 거래처명
		 , a.TaxGb		 		TAXGB			-- 과세구분
		 , a.Remark			 	REMARK			-- 비고
		 , a.InMgmNo 			INMGMNO			-- 입고등록관리번호
		 , ''					AUD
		 , ItemGrpCd            ITEMGRPCD       --품목군
	FROM   MISTW.dbo.tbPurIn a
	WHERE  a.InYmd BETWEEN @iInYm + '01' AND @iInYm + '31'	
	AND    a.InGb IN ('07', '08','11','12','13','14','15','16','17','18','19','20','21','22')
	AND    a.ItemGrpCd = @iItemGrpCd
	AND  ( ISNULL(@iCustCd, '') = '' OR (ISNULL(@iCustCd, '') <> '' AND a.CustCd = @iCustCd) )
	ORDER BY a.InYmd, a.CustCd, a.InMgmNo



END
```

<a id="mistw-proc-purc010-s-03"></a>
## `MISTW.dbo.proc_PURC010_S_03`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_PURC010_S_03.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_PURC010_S_03.sql

```sql




/********************************************************************************
제    목 : 품목 조회
프로그램 : 기타입고등록(기타)
등 록 일 : 2018-04-27
등 록 자 : 양장미
수정일		수정자		내용
20210708  이해권     플랙시블 품목 추가
-----------------------------------------------------------------------
EXEC proc_PURC010_S_03 '20181008', '07', 'CC085', '','','GP'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_PURC010_S_03]
		@iInYmd				varchar(8) = ''		-- 입고일자
	  , @iInGb				varchar(15) = ''	-- 입고구분
	  ,	@iCustCd			varchar(50) = ''	-- 발주거래처
	  , @iItemNm			varchar(100) = ''	-- 품목명
	  , @iSpec   			varchar(100) = ''	-- 규격
	  , @iItemGrpCd			varchar(10) = ''	-- 품목분류
AS

BEGIN

	SET NOCOUNT ON

	IF @iItemGrpCd ='SP'
	BEGIN 
	SELECT DISTINCT a.ItemNo		ITEMNO		-- 품목코드
		 , a.ItemNm					ITEMNM		-- 품목명
		 , a.Spec					SPEC		-- 규격
		 , ISNULL(c.Price, 0)		ITEMPRICE	-- 품목단가
		 , a.SortOrder
	FROM   MISPD.dbo.PDItemMaster a
	--INNER JOIN MISPD.dbo.PDItemProcess b ON a.ItemNo = b.ItemNo
	LEFT JOIN MISPD.dbo.tbItemPrice c ON c.ItemNo = a.ItemNo AND c.CustCd = @iCustCd AND c.PriceGb = '02'
									 AND c.ChgYmd = (SELECT MAX(x.ChgYmd) FROM MISPD.dbo.tbItemPrice x
													 WHERE  x.ItemNo = c.ItemNo AND x.CustCd = c.CustCd AND x.PriceGb = c.PriceGb AND x.ChgYmd <= @iInYmd)
	WHERE ( @iItemNm    is null or @iItemNm    = '' or  ( @iItemNm    is not null and @iItemNm    <> '' and a.ItemNm       LIKE '%' + @iItemNm + '%' ))
	  AND ( @iSpec      is null or @iSpec      = '' or  ( @iSpec      is not null and @iSpec      <> '' and a.Spec         LIKE '%' + @iSpec + '%' ))
	  AND ( @iItemGrpCd is null or @iItemGrpCd = '' or  ( @iItemGrpCd is not null and @iItemGrpCd <> '' and a.ItemGrpCd    =  @iItemGrpCd ))
	  AND AcctGb NOT IN ('HP','CP')
	  AND ISNULL(AcctGb,'')<>''
	/*  AND A.ItemNo IN 
	      ( 'XS1-HD00050','XS1-HD00055','YS1-HD00114','XS1-HD00051','XS1-HD00052','YS1-HD00116','XS1-HD00017','XS1-HD00015','XS1-HD00020','XS1-HD00009','XS1-M004','XS1-HD00022','XS1-HD00026','XS1-HD00030'
           ,'XS1-HD00031','XS1-HD00032','XS1-HD00033','XS1-HD00034','XS1-HD00021','VC1-SR100','VC1-SR13','VC1-SR20','VC1-SR25','VC1-SR30','VC1-SR40','VC1-SR50','VC1-SR60','VC1-SR75','VC1-SR80','VC1-AKK1003'
           ,'VC1-AKK1004','VC1-AKK101','VC1-AKK102','VC1-AKK103','VC1-AKK104','VC1-AKK105','VC1-AKK106','VC1-AKK107','VC1-AKK1071','VC1-AKK108','VC1-AKK109','VC1-AKL101','VC1-AKL102','VC1-AKL103','XS1-BVBHD13'
           ,'XS1-BVBHD20','XS1-BVBHD25','XS1-BVBHD30','XS1-BVBHD40','XS1-BVBHD50','XS1-BVBOR50','XS1-EP10101','XS1-EP10102','XS1-EP10103','XS1-EP10104','XS1-EP10106','XS1-EP10107','XS1-EP10108','XS1-EPMK100'
           ,'XS1-GTP104','XS1-GTP105','XS1-ZWCG040','XS1-ZWCGD25','XS1-ZWCGD30','YS1-HD00111','XS1-ZWCG001','YS1-HD00061','YS1-HD00062','YS1-HD00063','YS1-HD00064','YS1-HD00065','YS1-HD00066','YS1-HD00067'
           ,'YS1-HD00068','YS1-HD00069','YS1-HD00070','YS1-HD00071','YS1-HD00072','YS1-HD00073','YS1-HD00074','YS1-HD00075','YS1-HD00076','YS1-HD00077','YS1-HD00078','YS1-HD00079','YS1-HD00080','YS1-HD00081'
           ,'YS1-HD00082','YS1-HD00083','YS1-HD00084','YS1-HD00085','YS1-HD00086','YS1-HD00087','YS1-HD00088','YS1-HD00089','YS1-HD00090','YS1-HD00091','YS1-HD00092','YS1-HD00093','YS1-HD00094','YS1-HD00095'
           ,'YS1-HD00096','YS1-HD00097','YS1-HD00098','YS1-HD00099','YS1-HD00100','YS1-HD00118','YS1-HD00119','YS1-HD00125','YS1-HD00126','YS1-HD00127','YS1-HD00128','YS1-HD00129','YS1-HD00115','YS1-HD00110'
           ,'YS1-HD00107','YS1-HD00108','YS1-HD00102','YS1-HD00103','YS1-HD00104','YS1-HD00121','YS1-HD00131','YS1-HD00134','YS1-HD00141','YS1-HD00123','YS1-HD00120','XS1-HD00053','XS1-HD00054','XS1-HD00035'
           ,'XS1-HD00036','XS1-HD00037','XS1-HD00038','XS1-HD00039','XS1-HD00040','XS1-HD00041','XS1-HD00042','XS1-HD00043','XS1-HD00044','XS1-HD00045','YS1-HD00117','XS1-HD00011','XS1-HD00008','XS1-HD00023'
	       ,'XS1-HD00024','XS1-HD00025','XS1-HD00018','YS1-HD00112','WS2-DSP120','WS2-EPI0103','WS2-GTP103','WS2-GTP104','WS2-GTP105','WS2-GTP106','WS2-GTP108','WS2-ASP207','WS2-ASP208','WS2-BDR10','WS2-BDR11'
           ,'WS2-BDR12','WS2-AKK101','WS2-AKK102','WS2-AKK103','WS2-AKK104','WS2-AKK105','WS2-AKK106','WS2-AKK107','WS2-AKK108','WS2-AKK109','WS2-AKK110','WS2-AKK111','WS2-AKK112','WS2-AKK113','WS2-AKKS103'
           ,'WS2-AKKS104','WS2-AKKS105','WS2-AKKS106','WS2-AKKS107','WS2-AKKS108','WS2-AKKS110','WS2-AKL101','WS2-AKL102','WS2-AKL103','WS2-AKL6','XS1-HD00016','XS1-ZAA02','XS1-HD00010','YS1-HD00105','XS1-HD00046'
           ,'XS1-HD00047','XS1-HD00049','XS1-HD00056','XS1-HD00019','XS1-HD00012','XS1-HD00014','YS1-HD00101'
		   )
		   */
	ORDER BY a.ItemNo 
	END 
	ELSE IF @iItemGrpCd ='GP'
		BEGIN 

					SELECT DISTINCT a.ItemNo		ITEMNO		-- 품목코드
					 , a.ItemNm					ITEMNM		-- 품목명
					 , a.Spec					SPEC		-- 규격
					 , ISNULL(c.Price, 0)		ITEMPRICE	-- 품목단가
					 , a.SortOrder
				FROM   MISPD.dbo.PDItemMaster a
				--INNER JOIN MISPD.dbo.PDItemProcess b ON a.ItemNo = b.ItemNo
				LEFT JOIN MISPD.dbo.tbItemPrice c ON c.ItemNo = a.ItemNo AND c.CustCd = @iCustCd AND c.PriceGb = '02'
												 AND c.ChgYmd = (SELECT MAX(x.ChgYmd) FROM MISPD.dbo.tbItemPrice x
																 WHERE  x.ItemNo = c.ItemNo AND x.CustCd = c.CustCd AND x.PriceGb = c.PriceGb AND x.ChgYmd <= @iInYmd)
				WHERE ( @iItemNm    is null or @iItemNm    = '' or  ( @iItemNm    is not null and @iItemNm    <> '' and a.ItemNm       LIKE '%' + @iItemNm + '%' ))
				  AND ( @iSpec      is null or @iSpec      = '' or  ( @iSpec      is not null and @iSpec      <> '' and a.Spec         LIKE '%' + @iSpec + '%' ))
				  AND ( @iItemGrpCd is null or @iItemGrpCd = '' or  ( @iItemGrpCd is not null and @iItemGrpCd <> '' and a.ItemGrpCd    =  @iItemGrpCd ))
				--  AND ItemGb ='01'
				  AND AcctGb NOT IN ('HP','CP')
				  AND ISNULL(AcctGb,'')<>''
				ORDER BY a.ItemNo 
		END
	ELSE --@iItemGrpCd ='FP'
		BEGIN 

					SELECT DISTINCT a.ItemNo		ITEMNO		-- 품목코드
					 , a.ItemNm					ITEMNM		-- 품목명
					 , a.Spec					SPEC		-- 규격
					 , ISNULL(c.Price, 0)		ITEMPRICE	-- 품목단가
					 , a.SortOrder
				FROM   MISPD.dbo.PDItemMaster a
				--INNER JOIN MISPD.dbo.PDItemProcess b ON a.ItemNo = b.ItemNo
				LEFT JOIN MISPD.dbo.tbItemPrice c ON c.ItemNo = a.ItemNo AND c.CustCd = @iCustCd AND c.PriceGb = '02'
												 AND c.ChgYmd = (SELECT MAX(x.ChgYmd) FROM MISPD.dbo.tbItemPrice x
																 WHERE  x.ItemNo = c.ItemNo AND x.CustCd = c.CustCd AND x.PriceGb = c.PriceGb AND x.ChgYmd <= @iInYmd)
				WHERE ( @iItemNm    is null or @iItemNm    = '' or  ( @iItemNm    is not null and @iItemNm    <> '' and a.ItemNm       LIKE '%' + @iItemNm + '%' ))
				  AND ( @iSpec      is null or @iSpec      = '' or  ( @iSpec      is not null and @iSpec      <> '' and a.Spec         LIKE '%' + @iSpec + '%' ))
				  AND ( @iItemGrpCd is null or @iItemGrpCd = '' or  ( @iItemGrpCd is not null and @iItemGrpCd <> '' and a.ItemGrpCd    =  @iItemGrpCd ))
				--  AND ItemGb ='01'
				  --AND AcctGb NOT IN ('HP','CP')
				  AND ISNULL(AcctGb,'') <> ''
				ORDER BY a.ItemNo 
		END

END
```

<a id="mistw-proc-deliverystatus-s"></a>
## `MISTW.dbo.proc_DeliveryStatus_S`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_DeliveryStatus_S.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_DeliveryStatus_S.sql

```sql

/********************************************************************************
제    목 : 출고현황조회
프로그램 : [proc_DeliveryStatus_S]
등 록 일 : 2018-08-24
등 록 자 : 정재광
수정일		수정자		내용
-----------------------------------------------------------------------
proc_DeliveryStatus_S  @iItemGrpCd= 'GP'
,@iCustCd = '' 		
,@iEquipCustCd = '' 			
,@iSiteCD = '' 				
,@iDeliveryGb = '01'	
,@iContractGB = '02,03' 			
,@iGubun = '%' 				
EXEC MISTW.dbo.proc_DeliveryStatus_S @iDeliveryGb = '01', @iCustCd = '', @iEquipCustCD = '', @iSiteCD = '', @iContractGB = '02,03', @iGubun = '%'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_DeliveryStatus_S]
	 @iItemGrpCd	VARCHAR(2)	= 'SP'	-- 품목군
	,@iCustCd		VARCHAR(5)			-- 거래처
	,@iEquipCustCd	VARCHAR(50)			-- 설비업체
	,@iSiteCD		VARCHAR(50)			-- 현장명
	,@iDeliveryGb	VARCHAR(2)	= '01'	-- 출고구분
	,@iContractGB	VARCHAR(10) = '01'	-- 계약구분
	,@iGubun		VARCHAR(2)	= '%'	--
AS
BEGIN
	SET NOCOUNT ON;

	IF @iItemGrpCd = 'SP' BEGIN

		SELECT DeliveryNo DELIVERYNO
			 , RowNum
			 , MAX(ContractNo) CONTRACTNO
			 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(ContractYmd), '-') CONTRACTYMD
			 , CASE WHEN RowNum = 1 THEN MAX(ISNULL(ChargerTypeFL, 'N')) ELSE 'N' END CHARGERTYPEFL
			 , CASE WHEN RowNum = 1 THEN MAX(ISNULL(OilTypeFL, 'N')) ELSE 'N' END OILTYPEFL
			 , MAX(StandardFee) STANDARDFEE
			 , MAX(RebateAmt) REBATEAMT
			 , MAX(ContractFee) CONTRACTFEE
			 , CASE WHEN RowNum = 1 THEN MAX(ContractFee)  ELSE 0 END CONTRACTFEE_D
			 , CASE WHEN @iDeliveryGb = '02' OR RowNum = 1 THEN DeliveryGb ELSE '03' END DELIVERYGB
			 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(DeliveryYmd), '-') DELIVERYYMD
			 , MISCM.dbo.fn_gfnDateTypeTrans(MAX(ReturnYmd), '-') RETURNYMD
			 , CustCd CUSTCD
			 , MAX(EquipCustCd) EQUIPCUSTCD
			 , MAX(SiteCd) SITECD
			 , (SELECT CustNm FROM MISSA.dbo.SACust WHERE DuzCustCd = A.CustCd) CUSTNM
			 , (SELECT BusinessNm FROM MISSA.dbo.SABusiness WHERE BusinessCd = MAX(A.EquipCustCd)) EQUIPCUSTNM
			 , (SELECT SiteNm FROM MISTO..TOSIte WHERE SiteCd = MAX(A.SiteCd)) SITENM
			 , (SELECT PlaceNm FROM MISTO..TOPlace WHERE PlaceCd = MAX(A.DeliveryPlace)) DELIVERYPLACE
			 , CASE WHEN RowNum = 1 THEN MAX(Request) ELSE '' END REQUEST
			 , MAX(TelNo) TELNO
			 , MAX(DeliveryMoveNo) DELIVERYMOVENO
			 , MAX(DNO) DNO
			 , MAX(ContractGb) CONTRACTGB
			 , MAX(LotCnt) LOTCNT

			 --선출고 있는 건의 대체회수 여부(선출고품의 대체회수 못하기로, 원품목으로 대체회수)
			 , MAX(CASE WHEN TOOLNM = '죠우13'       THEN PreExChangeFl ELSE '' END)	[EXC_J013]
			 , MAX(CASE WHEN TOOLNM = '죠우20'       THEN PreExChangeFl ELSE '' END)	[EXC_J020]
			 , MAX(CASE WHEN TOOLNM = '죠우25'       THEN PreExChangeFl ELSE '' END)	[EXC_J025]
			 , MAX(CASE WHEN TOOLNM = '죠우30'       THEN PreExChangeFl ELSE '' END)	[EXC_J030]
			 , MAX(CASE WHEN TOOLNM = '죠우40'       THEN PreExChangeFl ELSE '' END)	[EXC_J040]
			 , MAX(CASE WHEN TOOLNM = '죠우50'       THEN PreExChangeFl ELSE '' END)	[EXC_J050]
			 , MAX(CASE WHEN TOOLNM = '죠우60'       THEN PreExChangeFl ELSE '' END)	[EXC_J060]
			 , MAX(CASE WHEN TOOLNM = '죠우75'       THEN PreExChangeFl ELSE '' END)	[EXC_J075]
			 , MAX(CASE WHEN TOOLNM = '죠우80'       THEN PreExChangeFl ELSE '' END)	[EXC_J080]
			 , MAX(CASE WHEN TOOLNM = '죠우100'      THEN PreExChangeFl ELSE '' END)	[EXC_J100]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN PreExChangeFl ELSE '' END)	[EXC_CLAMPM]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN PreExChangeFl ELSE '' END)	[EXC_CLAMPL]
			 , MAX(CASE WHEN TOOLNM = '건중형'       THEN PreExChangeFl ELSE '' END)	[EXC_GUNM]
			 , MAX(CASE WHEN TOOLNM = '건대형'       THEN PreExChangeFl ELSE '' END)	[EXC_GUNL]
			 , MAX(CASE WHEN TOOLNM = '실린더중형'   THEN PreExChangeFl ELSE '' END)	[EXC_CYLINDERM]
			 , MAX(CASE WHEN TOOLNM = '실린더대형'   THEN PreExChangeFl ELSE '' END)	[EXC_CYLINDERL]
			 , MAX(CASE WHEN TOOLNM = '충전기'       THEN PreExChangeFl ELSE '' END)	[EXC_CHARGER]
			 , MAX(CASE WHEN TOOLNM = '배터리1'      THEN PreExChangeFl ELSE '' END)	[EXC_BATTER]
			 , MAX(CASE WHEN TOOLNM = '배터리2'      THEN PreExChangeFl ELSE '' END)	[EXC_BATTER2]
			 , MAX(CASE WHEN TOOLNM = '펌프'         THEN PreExChangeFl ELSE '' END)	[EXC_PUMP]
			 , MAX(CASE WHEN TOOLNM = '절단기'       THEN PreExChangeFl ELSE '' END)	[EXC_CUTTER]

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

			 , MAX(CASE WHEN TOOLNM = '죠우13'			THEN LotNo2 ELSE '' END)	[LOTNO_J013]
			 , MAX(CASE WHEN TOOLNM = '죠우20'			THEN LotNo2 ELSE '' END)	[LOTNO_J020]
			 , MAX(CASE WHEN TOOLNM = '죠우25'			THEN LotNo2 ELSE '' END)	[LOTNO_J025]
			 , MAX(CASE WHEN TOOLNM = '죠우30'			THEN LotNo2 ELSE '' END)	[LOTNO_J030]
			 , MAX(CASE WHEN TOOLNM = '죠우40'			THEN LotNo2 ELSE '' END)	[LOTNO_J040]
			 , MAX(CASE WHEN TOOLNM = '죠우50'			THEN LotNo2 ELSE '' END)	[LOTNO_J050]
			 , MAX(CASE WHEN TOOLNM = '죠우60'			THEN LotNo2 ELSE '' END)	[LOTNO_J060]
			 , MAX(CASE WHEN TOOLNM = '죠우75'			THEN LotNo2 ELSE '' END)	[LOTNO_J075]
			 , MAX(CASE WHEN TOOLNM = '죠우80'			THEN LotNo2 ELSE '' END)	[LOTNO_J080]
			 , MAX(CASE WHEN TOOLNM = '죠우100'			THEN LotNo2 ELSE '' END)	[LOTNO_J100]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴중형'		THEN LotNo2 ELSE '' END)	[LOTNO_CLAMPM]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴대형'		THEN LotNo2 ELSE '' END)	[LOTNO_CLAMPL]
			 , MAX(CASE WHEN TOOLNM = '건중형'			THEN LotNo2 ELSE '' END)	[LOTNO_GUNM]
			 , MAX(CASE WHEN TOOLNM = '건대형'			THEN LotNo2 ELSE '' END)	[LOTNO_GUNL]
			 , MAX(CASE WHEN TOOLNM = '실린더중형'		THEN LotNo2 ELSE '' END)	[LOTNO_CYLINDERM]
			 , MAX(CASE WHEN TOOLNM = '실린더대형'		THEN LotNo2 ELSE '' END)	[LOTNO_CYLINDERL]
			 , MAX(CASE WHEN TOOLNM = '충전기'			THEN LotNo2 ELSE '' END)	[LOTNO_CHARGER]
			 , MAX(CASE WHEN TOOLNM = '배터리1'			THEN LotNo2 ELSE '' END)	[LOTNO_BATTER]
			 , MAX(CASE WHEN TOOLNM = '배터리2'			THEN LotNo2 ELSE '' END)	[LOTNO_BATTER2]
			 , MAX(CASE WHEN TOOLNM = '펌프'				THEN LotNo2 ELSE '' END)	[LOTNO_PUMP]
			 , MAX(CASE WHEN TOOLNM = '절단기'			THEN LotNo2 ELSE '' END)	[LOTNO_CUTTER]

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
			 , MAX(CASE WHEN TOOLNM = '죠우13'       THEN PreCnt ELSE 0 END)	[PCNT_J013]
			 , MAX(CASE WHEN TOOLNM = '죠우20'       THEN PreCnt ELSE 0 END)	[PCNT_J020]
			 , MAX(CASE WHEN TOOLNM = '죠우25'       THEN PreCnt ELSE 0 END)	[PCNT_J025]
			 , MAX(CASE WHEN TOOLNM = '죠우30'       THEN PreCnt ELSE 0 END)	[PCNT_J030]
			 , MAX(CASE WHEN TOOLNM = '죠우40'       THEN PreCnt ELSE 0 END)	[PCNT_J040]
			 , MAX(CASE WHEN TOOLNM = '죠우50'       THEN PreCnt ELSE 0 END)	[PCNT_J050]
			 , MAX(CASE WHEN TOOLNM = '죠우60'       THEN PreCnt ELSE 0 END)	[PCNT_J060]
			 , MAX(CASE WHEN TOOLNM = '죠우75'       THEN PreCnt ELSE 0 END)	[PCNT_J075]
			 , MAX(CASE WHEN TOOLNM = '죠우80'       THEN PreCnt ELSE 0 END)	[PCNT_J080]
			 , MAX(CASE WHEN TOOLNM = '죠우100'      THEN PreCnt ELSE 0 END)	[PCNT_J100]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN PreCnt ELSE 0 END)	[PCNT_CLAMPM]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN PreCnt ELSE 0 END)	[PCNT_CLAMPL]
			 , MAX(CASE WHEN TOOLNM = '건중형'       THEN PreCnt ELSE 0 END)	[PCNT_GUNM]
			 , MAX(CASE WHEN TOOLNM = '건대형'       THEN PreCnt ELSE 0 END)	[PCNT_GUNL]
			 , MAX(CASE WHEN TOOLNM = '실린더중형'   THEN PreCnt ELSE 0 END)	[PCNT_CYLINDERM]
			 , MAX(CASE WHEN TOOLNM = '실린더대형'   THEN PreCnt ELSE 0 END)	[PCNT_CYLINDERL]
			 , MAX(CASE WHEN TOOLNM = '충전기'       THEN PreCnt ELSE 0 END)	[PCNT_CHARGER]
			 , MAX(CASE WHEN TOOLNM = '배터리1'      THEN PreCnt ELSE 0 END)	[PCNT_BATTER]		
			 , MAX(CASE WHEN TOOLNM = '배터리2'      THEN PreCnt ELSE 0 END)	[PCNT_BATTER2]
			 , MAX(CASE WHEN TOOLNM = '펌프'         THEN PreCnt ELSE 0 END)	[PCNT_PUMP]		
			 , MAX(CASE WHEN TOOLNM = '절단기'       THEN PreCnt ELSE 0 END)	[PCNT_CUTTER]

			 --분실
			 , MAX(CASE WHEN TOOLNM = '죠우13'       THEN [Status] ELSE 0 END)	[LCNT_J013]
			 , MAX(CASE WHEN TOOLNM = '죠우20'       THEN [Status] ELSE 0 END)	[LCNT_J020]
			 , MAX(CASE WHEN TOOLNM = '죠우25'       THEN [Status] ELSE 0 END)	[LCNT_J025]
			 , MAX(CASE WHEN TOOLNM = '죠우30'       THEN [Status] ELSE 0 END)	[LCNT_J030]
			 , MAX(CASE WHEN TOOLNM = '죠우40'       THEN [Status] ELSE 0 END)	[LCNT_J040]
			 , MAX(CASE WHEN TOOLNM = '죠우50'       THEN [Status] ELSE 0 END)	[LCNT_J050]
			 , MAX(CASE WHEN TOOLNM = '죠우60'       THEN [Status] ELSE 0 END)	[LCNT_J060]
			 , MAX(CASE WHEN TOOLNM = '죠우75'       THEN [Status] ELSE 0 END)	[LCNT_J075]
			 , MAX(CASE WHEN TOOLNM = '죠우80'       THEN [Status] ELSE 0 END)	[LCNT_J080]
			 , MAX(CASE WHEN TOOLNM = '죠우100'      THEN [Status] ELSE 0 END)	[LCNT_J100]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN [Status] ELSE 0 END)	[LCNT_CLAMPM]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN [Status] ELSE 0 END)	[LCNT_CLAMPL]
			 , MAX(CASE WHEN TOOLNM = '건중형'       THEN [Status] ELSE 0 END)	[LCNT_GUNM]
			 , MAX(CASE WHEN TOOLNM = '건대형'       THEN [Status] ELSE 0 END)	[LCNT_GUNL]
			 , MAX(CASE WHEN TOOLNM = '실린더중형'   THEN [Status] ELSE 0 END)	[LCNT_CYLINDERM]
			 , MAX(CASE WHEN TOOLNM = '실린더대형'   THEN [Status] ELSE 0 END)	[LCNT_CYLINDERL]
			 , MAX(CASE WHEN TOOLNM = '충전기'       THEN [Status] ELSE 0 END)	[LCNT_CHARGER]
			 , MAX(CASE WHEN TOOLNM = '배터리1'      THEN [Status] ELSE 0 END)	[LCNT_BATTER]
			 , MAX(CASE WHEN TOOLNM = '배터리2'      THEN [Status] ELSE 0 END)	[LCNT_BATTER2]
			 , MAX(CASE WHEN TOOLNM = '펌프'         THEN [Status] ELSE 0 END)	[LCNT_PUMP]		
			 , MAX(CASE WHEN TOOLNM = '절단기'       THEN [Status] ELSE 0 END)	[LCNT_CUTTER]

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
			 , MAX(CASE WHEN TOOLNM = '클램핑툴중형'  THEN Seq ELSE '' END)		[SEQ_CLAMPM]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴대형'  THEN Seq ELSE '' END)		[SEQ_CLAMPL]
			 , MAX(CASE WHEN TOOLNM = '건중형'		 THEN Seq ELSE '' END)		[SEQ_GUNM]
			 , MAX(CASE WHEN TOOLNM = '건대형'		 THEN Seq ELSE '' END)		[SEQ_GUNL]
			 , MAX(CASE WHEN TOOLNM = '실린더중형'	 THEN Seq ELSE '' END)		[SEQ_CYLINDERM]
			 , MAX(CASE WHEN TOOLNM = '실린더대형'	 THEN Seq ELSE '' END)		[SEQ_CYLINDERL]
			 , MAX(CASE WHEN TOOLNM = '충전기'		 THEN Seq ELSE '' END)		[SEQ_CHARGER]
			 , MAX(CASE WHEN TOOLNM = '배터리1'		 THEN Seq ELSE '' END)		[SEQ_BATTER]
			 , MAX(CASE WHEN TOOLNM = '배터리2'		 THEN Seq ELSE '' END)		[SEQ_BATTER2]
			 , MAX(CASE WHEN TOOLNM = '펌프'			 THEN Seq ELSE '' END)		[SEQ_PUMP]
			 , MAX(CASE WHEN TOOLNM = '절단기'		 THEN Seq ELSE '' END)		[SEQ_CUTTER]

			 --미회수
			 , MAX(CASE WHEN TOOLNM = '죠우13'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J013]
			 , MAX(CASE WHEN TOOLNM = '죠우20'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J020]
			 , MAX(CASE WHEN TOOLNM = '죠우25'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J025]
			 , MAX(CASE WHEN TOOLNM = '죠우30'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J030]
			 , MAX(CASE WHEN TOOLNM = '죠우40'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J040]
			 , MAX(CASE WHEN TOOLNM = '죠우50'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J050]
			 , MAX(CASE WHEN TOOLNM = '죠우60'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J060]
			 , MAX(CASE WHEN TOOLNM = '죠우75'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J075]
			 , MAX(CASE WHEN TOOLNM = '죠우80'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J080]
			 , MAX(CASE WHEN TOOLNM = '죠우100'		 THEN UnRecoveredFl ELSE '' END)	[UNR_J100]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴중형' THEN UnRecoveredFl ELSE '' END)		[UNR_CLAMPM]
			 , MAX(CASE WHEN TOOLNM = '클램핑툴대형' THEN UnRecoveredFl ELSE '' END)		[UNR_CLAMPL]
			 , MAX(CASE WHEN TOOLNM = '건중형'		 THEN UnRecoveredFl ELSE '' END)	[UNR_GUNM]
			 , MAX(CASE WHEN TOOLNM = '건대형'		 THEN UnRecoveredFl ELSE '' END)	[UNR_GUNL]
			 , MAX(CASE WHEN TOOLNM = '실린더중형'	 THEN UnRecoveredFl ELSE '' END)	[UNR_CYLINDERM]
			 , MAX(CASE WHEN TOOLNM = '실린더대형'	 THEN UnRecoveredFl ELSE '' END)	[UNR_CYLINDERL]
			 , MAX(CASE WHEN TOOLNM = '충전기'		 THEN UnRecoveredFl ELSE '' END)	[UNR_CHARGER]
			 , MAX(CASE WHEN TOOLNM = '배터리1'		 THEN UnRecoveredFl ELSE '' END)	[UNR_BATTER]
			 , MAX(CASE WHEN TOOLNM = '배터리2'		 THEN UnRecoveredFl ELSE '' END)	[UNR_BATTER2]
			 , MAX(CASE WHEN TOOLNM = '펌프'		 THEN UnRecoveredFl ELSE '' END)		[UNR_PUMP]
			 , MAX(CASE WHEN TOOLNM = '절단기'		 THEN UnRecoveredFl ELSE '' END)	[UNR_CUTTER]
		FROM ( SELECT DeliveryNo, ContractNo, StandardFee, RebateAmt, ContractFee, DeliveryYmd, CustCd, EquipCustCd, SiteCd, DeliveryPlace, TelNo, Request
					, LotNo, LotCnt, PreExChangeFl, PreCnt, ToolNm, ReturnYmd
					, ROW_NUMBER() OVER(PARTITION BY DeliveryNo, ToolNm ORDER BY PreLotNo, LotNo) RowNum
					, DeliveryGb, DeliveryMoveNo, [Status], DNO, ContractGb, Seq, UnRecoveredFl, ContractYmd, ChargerTypeFL, OilTypeFL,LotNo2
			   FROM ( SELECT DM.DeliveryNo, DM.DeliveryYmd, DM.DeliveryGb, DM.CustCd, DM.EquipCustCd, DM.SiteCd, DM.DeliveryPlace, DM.TelNo, DM.Request
						   , CM.ContractNo, CM.StandardFee, CM.RebateAmt, CM.ContractFee, CM.ContractYmd, CM.ContractGb, CM.ReturnYmd
						   , CM.ChargerTypeFL, CM.OilTypeFL
						   , DD.PreLotNo, DD.Seq
						   , TMO.DeliveryMoveNo, TMO2.DeliveryNo DNO
						   , CASE WHEN R.DeliveryNo IS NOT NULL THEN '회수' ELSE DD.LotNo END LotNo
						   , DD.LotNo AS LotNo2
						   , CASE WHEN Lo.DeliveryNo IS NOT NULL THEN 1 ELSE 0 END [Status]
						   , CASE WHEN R.DeliveryNo IS NOT NULL THEN 0 WHEN Lo.DeliveryNo IS NOT NULL THEN 0 ELSE 1 END LotCnt
						   , DD.PreExChangeFl, ISNULL(DD.UnRecoveredFl, '') UnRecoveredFl
						   --AS후 동일 현장에 재출고되는 건 처리를 위해 PreSeq 관리해야하므로 변경
						   , (CASE WHEN EXISTS(SELECT 1 FROM MISTO..TODeliveryDetail WHERE DeliveryNo = DD.PreDeliveryNo AND Seq = DD.PreSeq)
									--PreSeq처리 안되는 기존 자료를 위해 남겨둠
									 OR EXISTS(SELECT 1 FROM MISTO..TODeliveryDetail WHERE DeliveryNo = DD.PreDeliveryNo AND LotNo = DD.PreLotNo) THEN 1 ELSE 0 END) PreCnt
						   , C.ClassCd, C.ClassLNm, C.ClassMNm, C.ClassSNm, C.Spec
						   , MISTO.dbo.FN_GetToolNm(DD.LotNo) ToolNm
					  FROM   MISTO..TOContractMaster CM
					  INNER JOIN MISTO..TODeliveryMaster DM ON DM.ContractNo = CM.ContractNo
					  LEFT JOIN MISTO..TODeliveryDetail DD ON DD.DeliveryNo = DM.DeliveryNo
					  LEFT JOIN MISTO..ToToolMaster TM ON TM.LotNo = DD.LotNo AND TM.ItemGrpCd = DD.ItemGrpCd
					  LEFT JOIN MISTO..TOClass C ON C.ClassCd = TM.ClassCd AND C.ItemGrpCd = TM.ItemGrpCD
					  LEFT JOIN MISTO..TORecovery R ON R.DeliveryNo = DD.DeliveryNo AND R.Seq = DD.Seq AND R.LotNo = DD.LotNo
					  LEFT JOIN MISTO..TOMove TMO ON TMO.DeliveryMoveNo = DD.DeliveryNo
					  LEFT JOIN MISTO..TOMove TMO2 ON TMO2.DeliveryNo = DD.DeliveryNo
					  LEFT JOIN MISTO..ToLoss Lo ON Lo.DeliveryNo = DD.DeliveryNo AND Lo.Seq = DD.Seq AND Lo.CancelYN != 'Y'
					  WHERE CM.ItemGrpCd = 'SP' 
					  AND	CASE WHEN @iDeliveryGb = '' THEN '01' ELSE @iDeliveryGb END = '01'
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
						   , DD.LotNo
						   , 0
						   , CASE WHEN R.DeliveryNo IS NOT NULL THEN 0 ELSE 1 END LotCnt
						   , DD.PreExChangeFl, ISNULL(DD.UnRecoveredFl, '') UnRecoveredFl
						   , 0
						   , C.ClassCd, C.ClassLNm, C.ClassMNm, C.ClassSNm, C.Spec
						   , MISTO.dbo.FN_GetToolNm(DD.LotNo) ToolNm
					  FROM   MISTO..TODeliveryMaster DM
					  INNER JOIN MISTO..TODeliveryDetail DD ON DD.DeliveryNo = DM.DeliveryNo
					  INNER JOIN MISTO..TOToolMaster TM ON TM.LotNo = DD.LotNo AND TM.ItemGrpCd = DD.ItemGrpCd
					  INNER JOIN MISTO..TOClass C ON C.ClassCd = TM.ClassCd AND C.ItemGrpCd = TM.ItemGrpCd
					  LEFT JOIN MISTO..TORecovery R ON R.DeliveryNo = DD.DeliveryNo AND R.Seq = DD.Seq
					  WHERE  DM.ItemGrpCd = 'SP' 
					  AND	DM.DeliveryGb = '02'
					  AND    @iDeliveryGb = '02'
					  AND    DM.CustCd = CASE WHEN @iCustCd = '' THEN DM.CustCd ELSE @iCustCd END
					  AND    DM.EquipCustCd = CASE WHEN @iEquipCustCd = '' THEN DM.EquipCustCd ELSE @iEquipCustCd END
					  AND    DM.SiteCd = CASE WHEN @iSiteCD = '' THEN DM.SiteCd ELSE @iSiteCD END
					  ) T
			 ) A
		WHERE  CustCd LIKE @iCustCd + '%'
		AND    EquipCustCd LIKE @iEquipCustCd + '%'
		AND    SiteCd LIKE @iSiteCD + '%'
		AND    CASE WHEN @iDeliveryGb = '02' OR RowNum = 1 THEN DeliveryGb ELSE '03' END LIKE @iGubun
		GROUP BY DeliveryNo, RowNum, CustCd, DeliveryGb
		ORDER BY DeliveryYmd
		--ORDER BY DeliveryNo
	END
	ELSE BEGIN
		SELECT A.DELIVERYNO, A.ROWNUM
			, MAX(A.DELIVERYGB)											AS DELIVERYGB		--출고구분
			, MAX(A.CONTRACTNO)											AS CONTRACTNO		--계약번호
			, MISCM.dbo.fn_gfnDateTypeTrans(MAX(A.DELIVERYYMD), '-')	AS DELIVERYYMD		--출고일자

			, MISCM.dbo.fn_gfnDateTypeTrans(MAX(CM.ContractYmd), '-')	AS CONTRACTYMD		--계약일자
			, MISCM.dbo.fn_gfnDateTypeTrans(MAX(Cm.ReturnYmd), '-')		AS RETURNYMD		--종료일자
			, CASE WHEN ROWNUM = 1 THEN ISNULL(MAX(CM.ContractFee), 0) ELSE 0 END		AS CONTRACTFEE		--임대료
			, MISTW.dbo.fn_comCustNm(MAX(A.CUSTCD))						AS CUSTNM			--거래처명
			, MISTW.dbo.fn_gtcEquipNm(MAX(A.EQUIPCUSTCD))				AS EQUIPCUSTNM		--설비업체명
			, MISTW.dbo.fn_gtcSiteNm(MAX(A.SITECD))						AS SITENM			--현장명
			, MAX(A.CUSTCD)												AS CUSTCD			--거래처코드
			, MAX(A.EQUIPCUSTCD)										AS EQUIPCUSTCD		--설비업체코드
			, MAX(A.SITECD)												AS SITECD			--현장코드
			, MAX(A.DELIVERYPLACE)										AS DELIVERYPLACE	--화물지점코드
			, MAX(A.TELNO)												AS TELNO			--전화번호
			, MAX(A.REQUEST)											AS REQUEST			--전달사항
			, MAX(A.Origin)												AS ORIGIN			--발송지
			, MAX(A.ReturnRmk)											AS RETURNRMK		--반납내역
			, SUM(A.LOTNO_CNT)											AS SUM_LOTNO		--소계
			--제품번호 및 상태(회수, 분실)
			, MAX(CASE WHEN TOOLNM = '상단롤러상단25'				THEN LOTNO ELSE '' END)	[LOUP025]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단32~40'			THEN LOTNO ELSE '' END)	[LOUP032]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단50~150'			THEN LOTNO ELSE '' END)	[LOUP050]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단100~150(전용)'	THEN LOTNO ELSE '' END)	[LOUP100]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단200~300'		THEN LOTNO ELSE '' END)	[LOUP200]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단25'				THEN LOTNO ELSE '' END)	[LODW025]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단32~40'			THEN LOTNO ELSE '' END)	[LODW032]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단50~150'			THEN LOTNO ELSE '' END)	[LODW050]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단100~150(전용)'	THEN LOTNO ELSE '' END)	[LODW100]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단200~300'		THEN LOTNO ELSE '' END)	[LODW200]
			, MAX(CASE WHEN TOOLNM = '샤프트상단상단'				THEN LOTNO ELSE '' END)	[SPUP]
			, MAX(CASE WHEN TOOLNM = '샤프트하단하단'				THEN LOTNO ELSE '' END)	[SPDW]
			, MAX(CASE WHEN TOOLNM = '펌프'						THEN LOTNO ELSE '' END)	[PUMP]
			, MAX(CASE WHEN TOOLNM = '그루브가공기'				THEN LOTNO ELSE '' END)	[GROV]
			, MAX(CASE WHEN TOOLNM = '파이프지지대볼타입'				THEN LOTNO ELSE '' END)	[PIPE]

			--SEQ
			, MAX(CASE WHEN TOOLNM = '상단롤러상단25'				THEN SEQ ELSE '' END)	[SEQ_LOUP025]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단32~40'			THEN SEQ ELSE '' END)	[SEQ_LOUP032]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단50~150'			THEN SEQ ELSE '' END)	[SEQ_LOUP050]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단100~150(전용)'	THEN SEQ ELSE '' END)	[SEQ_LOUP100]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단200~300'		THEN SEQ ELSE '' END)	[SEQ_LOUP200]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단25'				THEN SEQ ELSE '' END)	[SEQ_LODW025]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단32~40'			THEN SEQ ELSE '' END)	[SEQ_LODW032]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단50~150'			THEN SEQ ELSE '' END)	[SEQ_LODW050]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단100~150(전용)'	THEN SEQ ELSE '' END)	[SEQ_LODW100]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단200~300'		THEN SEQ ELSE '' END)	[SEQ_LODW200]
			, MAX(CASE WHEN TOOLNM = '샤프트상단상단'				THEN SEQ ELSE '' END)	[SEQ_SPUP]
			, MAX(CASE WHEN TOOLNM = '샤프트하단하단'				THEN SEQ ELSE '' END)	[SEQ_SPDW]
			, MAX(CASE WHEN TOOLNM = '펌프'						THEN SEQ ELSE '' END)	[SEQ_PUMP]
			, MAX(CASE WHEN TOOLNM = '그루브가공기'				THEN SEQ ELSE '' END)	[SEQ_GROV]
			, MAX(CASE WHEN TOOLNM = '파이프지지대볼타입'				THEN SEQ ELSE '' END)	[SEQ_PIPE]

			--대체회수여부
			, MAX(CASE WHEN TOOLNM = '상단롤러상단25'				THEN A.PreExChangeFl ELSE '' END)	[EXC_LOUP025]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단32~40'			THEN A.PreExChangeFl ELSE '' END)	[EXC_LOUP032]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단50~150'			THEN A.PreExChangeFl ELSE '' END)	[EXC_LOUP050]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단100~150(전용)'	THEN A.PreExChangeFl ELSE '' END)	[EXC_LOUP100]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단200~300'		THEN A.PreExChangeFl ELSE '' END)	[EXC_LOUP200]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단25'				THEN A.PreExChangeFl ELSE '' END)	[EXC_LODW025]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단32~40'			THEN A.PreExChangeFl ELSE '' END)	[EXC_LODW032]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단50~150'			THEN A.PreExChangeFl ELSE '' END)	[EXC_LODW050]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단100~150(전용)'	THEN A.PreExChangeFl ELSE '' END)	[EXC_LODW100]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단200~300'		THEN A.PreExChangeFl ELSE '' END)	[EXC_LODW200]
			, MAX(CASE WHEN TOOLNM = '샤프트상단상단'				THEN A.PreExChangeFl ELSE '' END)	[EXC_SPUP]
			, MAX(CASE WHEN TOOLNM = '샤프트하단하단'				THEN A.PreExChangeFl ELSE '' END)	[EXC_SPDW]
			, MAX(CASE WHEN TOOLNM = '펌프'						THEN A.PreExChangeFl ELSE '' END)	[EXC_PUMP]
			, MAX(CASE WHEN TOOLNM = '그루브가공기'				THEN A.PreExChangeFl ELSE '' END)	[EXC_GROV]
			, MAX(CASE WHEN TOOLNM = '파이프지지대볼타입'				THEN A.PreExChangeFl ELSE '' END)	[EXC_PIPE]

			--미회수품
			, MAX(CASE WHEN TOOLNM = '상단롤러상단25'				THEN A.UnRecoveredFl ELSE '' END)	[UNR_LOUP025]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단32~40'			THEN A.UnRecoveredFl ELSE '' END)	[UNR_LOUP032]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단50~150'			THEN A.UnRecoveredFl ELSE '' END)	[UNR_LOUP050]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단100~150(전용)'	THEN A.UnRecoveredFl ELSE '' END)	[UNR_LOUP100]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단200~300'		THEN A.UnRecoveredFl ELSE '' END)	[UNR_LOUP200]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단25'				THEN A.UnRecoveredFl ELSE '' END)	[UNR_LODW025]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단32~40'			THEN A.UnRecoveredFl ELSE '' END)	[UNR_LODW032]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단50~150'			THEN A.UnRecoveredFl ELSE '' END)	[UNR_LODW050]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단100~150(전용)'	THEN A.UnRecoveredFl ELSE '' END)	[UNR_LODW100]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단200~300'		THEN A.UnRecoveredFl ELSE '' END)	[UNR_LODW200]
			, MAX(CASE WHEN TOOLNM = '샤프트상단상단'				THEN A.UnRecoveredFl ELSE '' END)	[UNR_SPUP]
			, MAX(CASE WHEN TOOLNM = '샤프트하단하단'				THEN A.UnRecoveredFl ELSE '' END)	[UNR_SPDW]
			, MAX(CASE WHEN TOOLNM = '펌프'						THEN A.UnRecoveredFl ELSE '' END)	[UNR_PUMP]
			, MAX(CASE WHEN TOOLNM = '그루브가공기'				THEN A.UnRecoveredFl ELSE '' END)	[UNR_GROV]
			, MAX(CASE WHEN TOOLNM = '파이프지지대볼타입'				THEN A.UnRecoveredFl ELSE '' END)	[UNR_PIPE]

			--출고된 수량 파악
			, MAX(CASE WHEN TOOLNM = '상단롤러상단25'				THEN LOTNO_CNT ELSE 0 END)	[CNT_LOUP025]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단32~40'			THEN LOTNO_CNT ELSE 0 END)	[CNT_LOUP032]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단50~150'			THEN LOTNO_CNT ELSE 0 END)	[CNT_LOUP050]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단100~150(전용)'	THEN LOTNO_CNT ELSE 0 END)	[CNT_LOUP100]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단200~300'		THEN LOTNO_CNT ELSE 0 END)	[CNT_LOUP200]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단25'				THEN LOTNO_CNT ELSE 0 END)	[CNT_LODW025]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단32~40'			THEN LOTNO_CNT ELSE 0 END)	[CNT_LODW032]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단50~150'			THEN LOTNO_CNT ELSE 0 END)	[CNT_LODW050]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단100~150(전용)'	THEN LOTNO_CNT ELSE 0 END)	[CNT_LODW100]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단200~300'		THEN LOTNO_CNT ELSE 0 END)	[CNT_LODW200]
			, MAX(CASE WHEN TOOLNM = '샤프트상단상단'				THEN LOTNO_CNT ELSE 0 END)	[CNT_SPUP]
			, MAX(CASE WHEN TOOLNM = '샤프트하단하단'				THEN LOTNO_CNT ELSE 0 END)	[CNT_SPDW]
			, MAX(CASE WHEN TOOLNM = '펌프'						THEN LOTNO_CNT ELSE 0 END)	[CNT_PUMP]
			, MAX(CASE WHEN TOOLNM = '그루브가공기'				THEN LOTNO_CNT ELSE 0 END)	[CNT_GROV]
			, MAX(CASE WHEN TOOLNM = '파이프지지대볼타입'				THEN LOTNO_CNT ELSE 0 END)	[CNT_PIPE]

			--선출고 수량
			, MAX(CASE WHEN TOOLNM = '상단롤러상단25'				THEN PRE_CNT ELSE 0 END)	[PCNT_LOUP025]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단32~40'			THEN PRE_CNT ELSE 0 END)	[PCNT_LOUP032]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단50~150'			THEN PRE_CNT ELSE 0 END)	[PCNT_LOUP050]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단100~150(전용)'	THEN PRE_CNT ELSE 0 END)	[PCNT_LOUP100]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단200~300'		THEN PRE_CNT ELSE 0 END)	[PCNT_LOUP200]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단25'				THEN PRE_CNT ELSE 0 END)	[PCNT_LODW025]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단32~40'			THEN PRE_CNT ELSE 0 END)	[PCNT_LODW032]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단50~150'			THEN PRE_CNT ELSE 0 END)	[PCNT_LODW050]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단100~150(전용)'	THEN PRE_CNT ELSE 0 END)	[PCNT_LODW100]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단200~300'		THEN PRE_CNT ELSE 0 END)	[PCNT_LODW200]
			, MAX(CASE WHEN TOOLNM = '샤프트상단상단'				THEN PRE_CNT ELSE 0 END)	[PCNT_SPUP]
			, MAX(CASE WHEN TOOLNM = '샤프트하단하단'				THEN PRE_CNT ELSE 0 END)	[PCNT_SPDW]
			, MAX(CASE WHEN TOOLNM = '펌프'						THEN PRE_CNT ELSE 0 END)	[PCNT_PUMP]
			, MAX(CASE WHEN TOOLNM = '그루브가공기'				THEN PRE_CNT ELSE 0 END)	[PCNT_GROV]
			, MAX(CASE WHEN TOOLNM = '파이프지지대볼타입'				THEN PRE_CNT ELSE 0 END)	[PCNT_PIPE]

			--분실수량
			, MAX(CASE WHEN TOOLNM = '상단롤러상단25'				THEN LOSS_CNT ELSE 0 END)	[LCNT_LOUP025]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단32~40'			THEN LOSS_CNT ELSE 0 END)	[LCNT_LOUP032]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단50~150'			THEN LOSS_CNT ELSE 0 END)	[LCNT_LOUP050]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단100~150(전용)'	THEN LOSS_CNT ELSE 0 END)	[LCNT_LOUP100]
			, MAX(CASE WHEN TOOLNM = '상단롤러상단200~300'		THEN LOSS_CNT ELSE 0 END)	[LCNT_LOUP200]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단25'				THEN LOSS_CNT ELSE 0 END)	[LCNT_LODW025]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단32~40'			THEN LOSS_CNT ELSE 0 END)	[LCNT_LODW032]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단50~150'			THEN LOSS_CNT ELSE 0 END)	[LCNT_LODW050]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단100~150(전용)'	THEN LOSS_CNT ELSE 0 END)	[LCNT_LODW100]
			, MAX(CASE WHEN TOOLNM = '하단롤러하단200~300'		THEN LOSS_CNT ELSE 0 END)	[LCNT_LODW200]
			, MAX(CASE WHEN TOOLNM = '샤프트상단상단'				THEN LOSS_CNT ELSE 0 END)	[LCNT_SPUP]
			, MAX(CASE WHEN TOOLNM = '샤프트하단하단'				THEN LOSS_CNT ELSE 0 END)	[LCNT_SPDW]
			, MAX(CASE WHEN TOOLNM = '펌프'						THEN LOSS_CNT ELSE 0 END)	[LCNT_PUMP]
			, MAX(CASE WHEN TOOLNM = '그루브가공기'				THEN LOSS_CNT ELSE 0 END)	[LCNT_GROV]
			, MAX(CASE WHEN TOOLNM = '파이프지지대볼타입'				THEN LOSS_CNT ELSE 0 END)	[LCNT_PIPE]
		  FROM (
				SELECT DM.DeliveryNo, DM.ContractNo, DM.DeliveryYmd, DM.DeliveryGb, DM.CustCd, DM.EquipCustCd, DM.SiteCd, DM.DeliveryPlace, DM.TelNo
					, DM.Request, DM.Origin, DM.ReturnRmk
					, DD.Seq, DD.PreSeq, DD.PreLotNo					   
					, CASE WHEN R.DeliveryNo IS NOT NULL THEN '회수' ELSE DD.LotNo END LOTNO
					, CASE WHEN Lo.DeliveryNo IS NOT NULL THEN 1 ELSE 0 END	AS LOSS_CNT
					, CASE WHEN R.DeliveryNo IS NOT NULL OR Lo.DeliveryNo IS NOT NULL THEN 0 ELSE 1 END LOTNO_CNT
					, CASE WHEN ISNULL(DD.PreSeq, '') != '' THEN 1 ELSE 0 END PRE_CNT
					, DD.PreExChangeFl
					, ISNULL(DD.UnRecoveredFl, '') UnRecoveredFl					   
					, CASE WHEN ClassMCD IN ('04','05') THEN C.ClassMNm
						   ELSE C.ClassMNm + C.ClassSNm + C.Spec END AS TOOLNM
					, ROW_NUMBER() OVER(PARTITION BY DD.DeliveryNo, C.ClassMNm + C.ClassSNm + C.Spec ORDER BY DD.PreSeq, DD.Seq) AS ROWNUM
				  FROM MISTO..TODeliveryMaster DM 
				  JOIN MISTO..TODeliveryDetail DD ON DD.DeliveryNo = DM.DeliveryNo
				  JOIN MISTO..TOClass C ON C.ClassCd = DD.ClassCd AND C.ItemGrpCd = DD.ItemGrpCd
				  LEFT JOIN MISTO..TORecovery R ON R.DeliveryNo = DD.DeliveryNo AND R.Seq = DD.Seq AND R.LotNo = DD.LotNo
				  LEFT JOIN MISTO..ToLoss Lo ON Lo.DeliveryNo = DD.DeliveryNo AND Lo.Seq = DD.Seq AND Lo.CancelYN != 'Y'
				 WHERE DM.ItemGrpCd = 'GP' 
				   AND DM.DeliveryGb = @iDeliveryGb
				   AND DM.CustCd = CASE WHEN @iCustCd = '' THEN DM.CustCd ELSE @iCustCd END
				   AND DM.EquipCustCd = CASE WHEN @iEquipCustCd = '' THEN DM.EquipCustCd ELSE @iEquipCustCd END
				   AND DM.SiteCd  = CASE WHEN @iSiteCD = '' THEN DM.SiteCd  ELSE @iSiteCD END
			) A
		  LEFT JOIN MISTO..TOContractMaster CM ON CM.ContractNo = A.ContractNo
		 GROUP BY DeliveryNo, ROWNUM
		 ORDER BY DeliveryNo, ROWNUM

	END


END
```

<a id="mistw-proc-gtoc010-s-02"></a>
## `MISTW.dbo.proc_GTOC010_S_02`

화면·호출: 현재 `screen-sp.tsv` exact caller 미확인
원문 기준: **be_anasa develop 원문**
원본 경로: `/Users/cigro/Desktop/anasa/dst-onsite/be-repo/db/procedures/MISTW/proc_GTOC010_S_02.sql`
원격 원문: https://github.com/litmers-dev/be_anasa/blob/e3cb724616e872435f487d66c4703707d1ebb662/db/procedures/MISTW/proc_GTOC010_S_02.sql

```sql

/********************************************************************************
제    목 : 건AS현황_규격별AS현황
프로그램 : 건AS현황
등 록 일 : 2018-10-11
등 록 자 : 서도종
수정일	   수정자		내용
-----------------------------------------------------------------------
20211013	안진주		그리드 내에서 조회해야하는 항목 추가

EXEC proc_GTOC010_S_02 @iYy = '2018',@iGubun='건',@iITEMGRPCD = 'SP'
EXEC proc_GTOC010_S_02 @iYy = '2018',@iGubun='죠우',@iITEMGRPCD = 'SP'
EXEC proc_GTOC010_S_02 @iYy = '2018',@iGubun='클램핑툴',@iITEMGRPCD = 'SP'
*********************************************************************************/
CREATE PROCEDURE [dbo].[proc_GTOC010_S_02]
	 @iYy           VARCHAR(4) 
    ,@iGubun        VARCHAR(100) --건/죠우/클램핑툴 
	,@iItemGrpCd    VARCHAR(3) 
AS

DECLARE  @wErrYN			VARCHAR(1),
		 @wErrMsg		    VARCHAR(1000),
		 @wYyFr_0   		VARCHAR(4),
		 @wYyFr_1   		VARCHAR(4),
		 @wYyFr_2   		VARCHAR(4),
		 @wYyFr_3   		VARCHAR(4)
		
BEGIN
	SET @wYyFr_0 = @iYy - 0
	SET @wYyFr_1 = @iYy - 1
	SET @wYyFr_2 = @iYy - 2
	SET @wYyFr_3 = @iYy - 3


	SET NOCOUNT ON;

	IF @iGubun = '죠우'
		BEGIN
			SELECT X.FAULTCD
				,(SELECT FAULTNM FROM MISTO..TOFaultCode  WHERE FAULTCD = X.FAULTCD AND ItemGrpCd =@iItemGrpCd) AS FAULTNM
				, SUM(CNT)                                          AS SUM_CNT 
				, MAX(CASE WHEN X.SPEC='13'  THEN (CNT) ELSE 0 END) AS SPEC_13 
				, MAX(CASE WHEN X.SPEC='20'  THEN (CNT) ELSE 0 END) AS SPEC_20
				, MAX(CASE WHEN X.SPEC='25'  THEN (CNT) ELSE 0 END) AS SPEC_25 
				, MAX(CASE WHEN X.SPEC='30'  THEN (CNT) ELSE 0 END) AS SPEC_30 
				, MAX(CASE WHEN X.SPEC='40'  THEN (CNT) ELSE 0 END) AS SPEC_40 
				, MAX(CASE WHEN X.SPEC='50'  THEN (CNT) ELSE 0 END) AS SPEC_50 
				, MAX(CASE WHEN X.SPEC='60'  THEN (CNT) ELSE 0 END) AS SPEC_60 
				, MAX(CASE WHEN X.SPEC='75'  THEN (CNT) ELSE 0 END) AS SPEC_75 
				, MAX(CASE WHEN X.SPEC='80'  THEN (CNT) ELSE 0 END) AS SPEC_80 
				, MAX(CASE WHEN X.SPEC='100' THEN (CNT) ELSE 0 END) AS SPEC_100 
			FROM
			(SELECT B.FAULTCD      AS FAULTCD
					,COUNT(*)        AS CNT
					,D.Spec          AS SPEC
				FROM MISTO..TOAs A
				INNER JOIN MISTO..TOASFAULT B
				ON A.ASNO = B.ASNO 
				AND B.ItemGrpCd =@iItemGrpCd
				INNER JOIN MISTO..TOToolMaster C
				ON A.LOTNO = C.LOTNO
				AND C.ItemGrpCd =@iItemGrpCd
				INNER JOIN MISTO..TOClass D
				ON C.ClassCd   = D.ClassCd 
				AND D.ItemGrpCd =@iItemGrpCd    
				AND D.Gubun1    = @iGubun
				WHERE LEFT(InYmd,4) <=@iYy+'1231'
				AND A.ItemGrpCd =@iItemGrpCd
				GROUP BY B.FAULTCD
						,D.Spec
			) X
			GROUP BY X.FAULTCD
			ORDER BY FAULTCD 
		END
	
	ELSE IF @iGubun = '건'
		BEGIN
		    SELECT Z.FAULTCD
			     ,(SELECT FAULTNM FROM MISTO..TOFaultCode  WHERE FAULTCD = Z.FAULTCD AND ItemGrpCd =@iItemGrpCd) AS FAULTNM
				 ,SUM(Z.XX) +  SUM(Z.YY) + SUM(Z.BZ) + SUM(Z.AC) + SUM(Z.DM) + SUM(Z.ZD) AS SUM_CNT
				 ,SUM(Z.XX) AS XX_CNT
				 ,SUM(Z.YY) AS YY_CNT
				 ,SUM(Z.BZ) AS BZ_CNT
				 ,SUM(Z.AC) AS AC_CNT
				 ,SUM(Z.DM) AS DM_CNT
				 ,SUM(Z.ZD) AS ZD_CNT
            FROM 
		    (SELECT Y.FAULTCD 
			     , CASE WHEN X.ClassCd IN ('XX', 'RG') THEN (Y.ASCNT / X.OUTCNT) *100 ELSE 0 END AS XX
				 , CASE WHEN X.ClassCd = 'YY' THEN (Y.ASCNT / X.OUTCNT) *100 ELSE 0 END AS YY
				 , CASE WHEN X.ClassCd IN ('BZ','ZX','RP','ZS') THEN (Y.ASCNT / X.OUTCNT) *100 ELSE 0 END AS BZ
				 , CASE WHEN X.ClassCd = 'AC' THEN (Y.ASCNT / X.OUTCNT) *100 ELSE 0 END AS AC
				 , CASE WHEN X.ClassCd = 'DM' THEN (Y.ASCNT / X.OUTCNT) *100 ELSE 0 END AS DM
				 , CASE WHEN X.ClassCd IN ('SD','YD','ZD') THEN (Y.ASCNT / X.OUTCNT) *100 ELSE 0 END AS ZD
			  FROM 
				(SELECT A.ClassCd 
					 , CONVERT(decimal, COUNT(*))  AS OUTCNT
				  FROM MISTO..TODeliveryDetail A
				  INNER JOIN MISTO..TODeliveryMaster B 			   
				    ON A.DeliveryNo = B.DeliveryNo 
					AND B.DeliveryGb IN ('01','03') --임대/AS
				  WHERE A.ClassCd IN ('XX','RG','YY','BZ','ZX','RP','ZS','AC','DM','SD','YD','ZD')
					AND A.ItemGrpCd = @iItemGrpCd					
				  GROUP BY A.ClassCd 
				 )X		
				 INNER JOIN 
					 (SELECT B.FAULTCD      AS FAULTCD
							,D.ClassCd      AS CLASSCD 
							,CONVERT(decimal, COUNT(*))   AS ASCNT
						FROM MISTO..TOAs A
						INNER JOIN MISTO..TOASFAULT B
						ON A.ASNO = B.ASNO 
						AND B.ItemGrpCd =@iItemGrpCd
						INNER JOIN MISTO..TOToolMaster C
						ON A.LOTNO = C.LOTNO
						AND C.ItemGrpCd =@iItemGrpCd
						INNER JOIN MISTO..TOClass D
						ON C.ClassCd   = D.ClassCd 
						AND D.ItemGrpCd =@iItemGrpCd    
						AND D.Gubun1    = @iGubun
						AND D.ClassCd   IN ('XX','RG','YY','BZ','ZX','RP','ZS','AC','DM','SD','YD','ZD')
						WHERE LEFT(InYmd,4) <=@iYy+'1231'
						AND A.ItemGrpCd =@iItemGrpCd
						GROUP BY B.FAULTCD,D.ClassCd
				) Y
            ON X.ClassCd =Y.CLASSCD
			) Z	 
		GROUP BY Z.FAULTCD
		HAVING SUM(Z.XX) +  SUM(Z.YY) + SUM(Z.BZ) + SUM(Z.AC) + SUM(Z.DM) + SUM(Z.ZD) > 0
		END
END
```


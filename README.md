  override fun findReconcileSettlement(yearMonth: String): List<ReconcileSettlementReportDTO> {
        val databaseSchema = databaseConfig.scbdidAdminSchema
        val tableName = "reconcile_settlement_report"
        try {
            val query = """
                -- Reconcile PayTo Transaction

                SELECT	:yearMonth AS 'year_month',
                		CASE WHEN ndid.node_name_en = 'NDID' THEN CONCAT('NDID e-Stamp Fee', ' ( NDID = ',ndid.total_stamp,' , SCB :',scb.total_stamp,')')
                			 ELSE ndid.node_name_en
                		END AS 'node_name_en',
                		CASE WHEN ndid.node_name_en = 'NDID' THEN CONCAT('NDID e-Stamp Fee', ' ( NDID = ',ndid.total_stamp,' , SCB :',scb.total_stamp,')')
                			 WHEN ndid.node_name_en = 'MI MO TECH CO.,LTD.' THEN 'บริษัท ไมโม่เทค จำกัด'
                			 ELSE scb.node_name_th
                		END AS 'node_name_th',
                		scb.total_billing_transaction AS 'billing_transaction_from_scb' ,
                	   	ndid.total_billing_transaction AS 'billing_transaction_from_ndid',
                	   	(scb.total_billing_transaction-ndid.total_billing_transaction) AS 'diff',
                	   	ndid.total_fee AS 'amount',
                	   	ndid.vat_amount AS 'vat',
                	   	ndid.wht_amount AS 'wht',
                	   	ndid.net_amount AS 'grand_total',
                	   	(ndid.net_amount - ndid.wht_amount) AS 'account_balance',
                	   	ndid.transaction_type

                FROM (
                		SELECT
                			n.node_name AS node_name_en,
                			LOWER(REPLACE(IF(m.marketing_name IS NULL,n.node_name,m.marketing_name),' ','')) AS key_name,
                			n.total_billing_transaction,
                			n.transaction_type,
                			n.total_stamp,
                			n.total_fee,
                			n.vat_amount,
                			n.wht_amount,
                			n.net_amount
                		FROM scbdid_db_admin.ndid_summary_settlement_report AS n LEFT JOIN scbdid_db_admin.reconcile_settlement_mapping_node_name AS m ON m.node_name = n.node_name
                		WHERE n.`year_month` = :yearMonth
                		AND n.transaction_type = 'payTo'
                		AND (n.total_fee != 0 OR n.node_name = 'SIAM COMMERCIAL BANK PUBLIC COMPANY LIMITED')
                	) AS ndid LEFT JOIN
                	(
                		SELECT
                			s.node_name AS node_name_en,
                			idp.marketing_name_th AS node_name_th,
                			LOWER(IF (LOCATE(',',REPLACE(s.node_name,' ','')) BETWEEN 0 AND 10 , SUBSTRING(REPLACE(s.node_name,' ',''),LOCATE(',',REPLACE(s.node_name,' ','')) + 1 ),REPLACE(s.node_name,' ',''))) AS key_name,
                			s.total_billing_transaction,
                			s.total_stamp,
                			s.transaction_type,
                			s.total_fee,
                			s.vat_amount,
                			s.wht_amount,
                			s.net_amount
                			FROM scbdid_db_admin.summary_settlement_report s LEFT JOIN (SELECT marketing_name_th , marketing_name_en
                																		FROM scbdid_db_admin.idp_node_info i
                																		GROUP BY marketing_name_th , marketing_name_en) AS idp ON idp.marketing_name_en = s.node_name
                		WHERE s.`year_month` = :yearMonth
                		AND s.transaction_type = 'payTo'
                		AND (s.total_fee != 0 OR s.node_name = 'Siam Commercial Bank (SCB)')
                	) AS scb ON REPLACE(scb.key_name,'publiccompanylimited','') = REPLACE(ndid.key_name,'publiccompanylimited','')

                UNION ALL

                -- Reconcile BillTo Transaction

                SELECT 	:yearMonth AS 'year_month',
                		ndid.node_name_en AS 'node_name_en',
                		scb.node_name_th AS 'node_name_th',
                		scb.total_billing_transaction AS 'billing_transaction_from_scb' ,
                	   	ndid.total_billing_transaction AS 'billing_transaction_from_ndid',
                	   	(scb.total_billing_transaction-ndid.total_billing_transaction) AS 'diff',
                	   	ndid.total_fee AS 'amount',
                	   	ndid.vat_amount AS 'vat',
                	   	ndid.wht_amount AS 'wht',
                	   	ndid.net_amount AS 'grand_total',
                	   	(ndid.net_amount - ndid.wht_amount) AS 'account_balance',
                	   	ndid.transaction_type

                FROM (
                		SELECT
                			n.node_name AS node_name_en,
                			LOWER(REPLACE(IF(m.marketing_name IS NULL,n.node_name,m.marketing_name),' ','')) AS key_name,
                			n.transaction_type,
                			n.total_billing_transaction,
                			n.total_fee,
                			n.vat_amount,
                			n.wht_amount,
                			n.net_amount
                		FROM scbdid_db_admin.ndid_summary_settlement_report AS n LEFT JOIN scbdid_db_admin.reconcile_settlement_mapping_node_name AS m ON m.node_name = n.node_name
                		WHERE n.`year_month` = :yearMonth
                		AND n.transaction_type = 'billTo'
                		AND (n.node_type = 'IdP' AND n.node_name != 'THE REVENUE DEPARTMENT') OR (n.node_type = 'AS' AND n.node_name = 'THE REVENUE DEPARTMENT')
                	) AS ndid

                	LEFT JOIN

                	(
                		SELECT
                			s.node_name AS node_name_en,
                			rp.marketing_name_th AS node_name_th,
                			LOWER(IF (LOCATE(',',REPLACE(s.node_name,' ','')) BETWEEN 0 AND 10 , SUBSTRING(REPLACE(s.node_name,' ',''),LOCATE(',',REPLACE(s.node_name,' ','')) + 1 ),REPLACE(s.node_name,' ',''))) AS key_name,
                			s.total_billing_transaction,
                			s.transaction_type,
                			s.total_fee,
                			s.vat_amount,
                			s.wht_amount,
                			s.net_amount
                			FROM scbdid_db_admin.summary_settlement_report s LEFT JOIN (SELECT marketing_name_th , marketing_name_en
                																		FROM scbdid_db_admin.rp_node_info i
                																		GROUP BY marketing_name_th , marketing_name_en) AS rp ON s.node_name = rp.marketing_name_en
                		WHERE s.`year_month` = :yearMonth
                		AND s.transaction_type = 'billTo'
                		AND s.node_type = 'IdP'
                	) AS scb ON REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(scb.key_name,'(thailand)publiccompanylimited',''),'publiccompanylimited.',''),'publiccompanylimited',''),'(thailand)companylimited',''),'companylimited',''),'(thailand)co.,ltd.',''),'(thailand)limited',''),'(thailand)pcl.',''),'pcl.',''),'pcl',''),'co.,ltd.',''),'co.,ltd',''),'co.ltd','')
                			  = REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(REPLACE(ndid.key_name,'(thailand)publiccompanylimited',''),'(thailand)companylimited',''),'(thailand)publiccompany',''),'(thailand)co.,ltd.',''),'publiccompanylimited.',''),'publiccompanylimited',''),'companylimited',''),'co.,ltd.',''),'co.,ltd',''),'co.ltd',''),'pcl.',''),'pcl',''),'plc.','')

                ORDER BY transaction_type DESC , node_name_en ASC;
            """.trimIndent()
            val parameterSource = MapSqlParameterSource()
            parameterSource.addValue("yearMonth" , yearMonth)

        }
        catch (e: Exception) {
            logService.error(ErrorLogMessage(tableName, e))
            throw DatabaseException(e.message)
        }
    }

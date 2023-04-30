# make Aurora RDS Multi-AZ (for failover) and deploy Read Replica (for read performance) and promote Read Replica to 

## Enable Multi-AZ Deployment
### Configure Your Multi-AZ Instance
1. Navigate to EC2 using the Services menu or the unified search bar.
2. In the sidebar menu, scroll down to Load Balancing and select **Load Balancers**
3. Copy the DNS name of the load balancer.
4. Open a new browser tab and paste your copied DNS name. (You will use this web page to test failovers and promotions in this lab)
5. Go back to the AWS Management Console and navigate to RDS using the Services menu or the unified search bar.
6. In the Amazon RDS sidebar menu, select Databases.
7. Select the radio button to the left of your database details.
8. Along the top of the page, click Modify (The database's configuration options may take a moment to load)
9. Enable Multi-AZ deployment:
  - In the **Availability & Durability** section, select **Create a standby instance** (recommended for production usage).
  - Scroll to the bottom of the page and click Continue.
  - In the Schedule modifications section, select Apply immediately.
  - Click Modify DB instance.
10. After a few moments, click Refresh.
    You should see the Status change to Modifying. If you don't see this change, you may need to click Refresh again. The instance may take up to 10 minutes to enable Multi-AZ.
### Verify the Multi-AZ Deployment
1. After the instance is available again, verify that Multi-AZ is enabled:
  - In the Amazon RDS sidebar menu, select **Events**.
  - Review the events history for your instance.
  (You should see an event for converting the instance to Multi-AZ)
2. Reboot the instance to simulate the Multi-AZ redundancy:
  - In the Amazon RDS sidebar menu, select Databases.
  - Ensure the radio button to the left of your instance is selected.
  - Use the Actions dropdown to select Reboot.
  - On the Reboot DB Instance page, select **Reboot With Failover?** and click Confirm.
  This ensures the secondary instance takes over when this instance goes down. The reboot takes a few moments.
3. Navigate to the DNS web page tab and refresh the page.
   The web page should stay up because you have Multi-AZ deployment enabled. After the reboot is complete, the Multi-AZ standby is now the primary.
4. Navigate back to the AWS Management Console and use the Amazon RDS sidebar to select Events (you may need to expand the sidebar menu).
5. Review the events history for your instance.
   You should see events for the Multi-AZ failover listed.

---

## Create a Read Replica
1. In the Amazon RDS sidebar menu, select Databases to return to your instance details.
2. Ensure the radio button to the left of your instance is selected.
3. Use the Actions dropdown to select Create read replica.
4. In the Settings section, enter wordpress-rr into the DB instance identifier field.
5. In the AWS Region section, ensure the Destination Region is set to US East (N. Virginia).
6. Leave the other default settings and click Create read replica at the bottom of the page.
   (The read replica will take about 5-10 minutes to become available)
7. Navigate to the DNS web page tab and refresh the page.
   (The web page should stay up because the read replica changes are asynchronous)

---

## Promote the Read Replica and Change the CNAME Record in Route 53 to Point to the New Endpoint
### Promote the Read Replica
1. Navigate back to the AWS Management Console.
2. After the read replica is available, select the radio button to the left of it.
3. Use the Actions dropdown to select Promote.
4. Leave all the defaults and click Promote read replica at the bottom of the page.
5. After a few moments, click Refresh.
   (You should see that the read replica's Role has changed from *Replica* to *Instance*, and that the Status is now Modifying.)
### Change the CNAME Record to Point to the New Endpoint
1. After the read replica instance is available, select the instance name for the erstwhile Read Replica.
2. On the Connectivity & security tab, copy the Endpoint to your clipboard.
3. Open **Route 53** in a new tab.
4. In the Route 53 sidebar menu, select *Hosted zones*.
5. Select the *mydomain.local* hosted zone name.
   - In the hosted zone details, you should see a **CNAME record** that points to your database endpoint.
6. Check the checkbox to the left of the CNAME record.
7. In the Record details pane on the right, click Edit record.
8. Replace the existing Value with your copied read replica endpoint.
9. Click Save.
10. Navigate to the DNS web page tab and refresh the page.
   (You should see no downtime)

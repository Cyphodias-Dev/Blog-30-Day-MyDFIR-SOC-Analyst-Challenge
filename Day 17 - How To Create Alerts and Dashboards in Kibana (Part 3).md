## Day 17 - How To Create Alerts and Dashboards in Kibana (Part 3)
**Create a dashboard for RDP activity**

Similiar to Day 14 we're going to create a dashboard but this time for our RDP telemetry.

- Click on the hamburger menu and select maps.
- From our RDP filter we created we'll paste this into the filter field at the top `event.code: 4625 and agent.name: MYDFIR-WIN-(Your Handle)`
- Make sure our timeframe is set to "Last 7 days" at the top right.
- And we'll click on the blue "Add Layer" button.
- Select the "Choropleth" option in the list.
- In the EMS boundaries drop down select "World Countries".
- In the Data view drop down we'll pick the longest one that should be listed starting with "alerts-security.alerts-default,apm-*...",
- In the join field drop down we'll choose "source.geo.country_iso_code".
- Click the blue "Add and continue" button and the blue "Save" button at the top right.
- In the Save Map window that pops up we'll name it `RDP Failed Authentications`
- Under the "Existing" drop down we'll select our existing dashboard "MyDFIR Authentication Activity".
- Click on the blue "Save and go to Dashboard" button.

Now you should see a third map on your dashboard for failed RDP attampts.

![Elastic dashboard view with RDP failed map](https://github.com/user-attachments/assets/91cd79ac-6c65-4ebd-b4cd-47a693a11930)

Like our SSH maps let's make a successful RDP authentications map as well.  We'll need to look up the some additional info for Event ID 4624.

[https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4624](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventid=4624)

This page shows us there are two successful logon types for Event ID 4624 which we'll have to incorporate in our query.

- **Logon type 7**:  Unlock (i.e. unnattended workstation with password protected screen saver)
- **Logon type 10**:  RemoteInteractive (Terminal Services, Remote Desktop or Remote Assistance)

If we go back to our Discover panel we can filter at the top for just `event.code:4624` for the last 7 days.  Let's open one of the alerts and on the right hand panel we'll search at the top for "logon".  There should be one listing for "winlog.event_data.LogonType" with a value of 5.

Ultimatesecurity.com lists logon type 5 as "Service (Service startup)".  So this looks like what we want.  We'll add the following to our filter to look for logon types 10 & 7.
`event.code: 4624 and (winlog.event_data.LogonType: 10 or winlog.event_data.LogonType: 7)`.  and if you've logged into your Windows VM in the past week (Which I hope you have if you're participating in the challenge) you should see a logon attempt from your own host PC.  Let's save this query so we have it for future use.  Click the blue "Save" button at the top and we'll call it `RDP Successful Activity`.  Toggle the option on at the bottom left "Save as new search" and then click the blue "Save" button at the bottom right.

Now we'll go back to our dashboard and on the bottom "RDP Failed Authentication" map we'll click on the three dots at the top right corner of the map and select duplicate.  We'll rename the title in the map on the right by click on the title.  We'll call it `RDP Successful Authentications` and we'll edit our query down below to the one we created above.
`event.code: 4624 and (winlog.event_data.LogonType: 10 or winlog.event_data.LogonType: 7)`. and click on the blue "Save and return" button at the top right.

**Table Creation**

The next thing we can do is show the info from the maps in a table format.  This will enhance the data on the maps and make it easier to read at a glance.  Back on our dashboard with our four maps we'll perform the following steps:

- Click on the blue "Create Visualization" button at the top left.
- Find your failed SSH query and paste it into the filer field at the top and click refresh at the top right.  eg. query `system.auth.ssh.event : * and agent.name: MYDFIR-Linux-(Your Handle) and system.auth.ssh.event: Failed`
- Adjust the timeframe to "Last 7 Days" on the top right.
- On the right hand side change the drop down from "Bar vertical stacked" to "Table".
- Find the following fields on the left and drag them to the center. (user.name, source.ip, source.geo.country_name).
- On the right in the "Rows" adjust the columns so it's in the order we added them in again (user.name, source.ip, source.geo.country_name).
- In "Rows" on the right click on the "Top 3 values of user.name" and change the "Number of values" to 10.  Under Advanced un-toggle the "Group remaining values as 'Other'".
- Just like the last step peform ths for source.ip and source.geo.country_name as well.
- Click the blue "Save and return" button at the top right.

Back on our dasboard you should have a table.  We'll name our table by clicking on the title bar, where it says, "[No Title]" and give it a name of `SSH Failed Authentications [Table]` and click the blue "Apply" button at the bottom right.  Now we can drag this above so it's just below our SSH Failed Authentications map by dragging it with the tile bar.

We can create a successful table just like we did with the maps by clicking on the three dots at the top right of the table and selecting "Duplicate".  We'll rename the new table as `SSH Successful Authentications [Table]` and down below on the same window we'll click on "Edit" by Query and change the query string at the top by changing "Failed" at the end to "Accepted" and click on the blue "Save and return" button at the top right.

**Note** if some of your tables or maps are different sizes, or you just want to adjust them, you can so by dragging the bottom right hand corner (Your mouse icon should change to a diagonal double arrow when you hover over the corner).

Now we can do the same thing for RDP by dupicating the failed SSH table.  Drag the table to the bottom left below the RDP Failed Authentications map and we'll rename it to `RDP Failed Authentications [Table]`. And, like the above step, we'll edit the query below in the same window.  Change the query to our RDP query string `event.code: 4625 and agent.name: MYDFIR-WIN-(Your Handle)` and click on the blue "Save and return" button.

To duplicate the successful table we'll first copy the query string from the RDP Succssful map `event.code: 4624 and (winlog.event_data.LogonType: 10 or winlog.event_data.LogonType: 7)`.  We'll duplicate the failed RDP table and edit the table name and query string in the duplicated table.  When you're all done make sure to hit the blue "Save" button at the top right to save all your work.

You should now have 4 maps and 4 tables for SSH and RDP to view all the brute force attacks.

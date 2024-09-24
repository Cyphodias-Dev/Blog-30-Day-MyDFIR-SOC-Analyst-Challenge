## Day 16 - How To Create Alerts and Dashboards in Kibana (Port 2)
**Objective:  Observe authentication logs from our Windows server and create an alert.**

**Create RDP Brute Force Alert**

- Click on the Hamburger menu and select Discover.
- Ensure your timeframe is set for "Today".  Take note of how many alerts you have listed right above your timestamp column of alerts (eg. Documents (89,420)).
- Click on the agent.name on the left hand side and choose the plus button for your MyDFIR Windows listing.
- Notice how many alerts we've filtered down to (eg. Documents (30,368)).

Just like in our SSH brute force filter we're looking for the same three fields:
- Failed attempts
- User
- Source IP

Now we need to find out what Event ID is associated with failed authentication for Windows.  Lets search the internet for `failed authentication event id windows`.
We get the following result:

**Event ID 4625:** An account failed to log on (local computer)
- This event is generated on the computer where the logon attempt was made.
- It provides information about the account name, logon type (e.g., interactive, network), and the reason for failure.

Event ID 4625 is what we're looking for.  Let's try filtering for this Event ID in Elastic.  We'll just type "4625" in the filter box at the top.  Nice!  This should filter the events down even further (eg. Documents (5,044)).  If you open one of the alerts let's see what field event ID 4625 is under. 

![Elastic event.code field](https://github.com/user-attachments/assets/b38b96d4-70ea-4899-8380-40bf5edbe521)

So event.code is what we want.  (** Note:  event.code can also be seen before the highlighted event id 4625 in each alert). To confirm this we can add this to the filter field at the top where we entered our event id 4625 earlier.  So we'll enter `event.code: 4625`.

Now let's add source IP to our filter in the search box under all the fields on the left hand side we'll  search for `source.ip`.  If we click on the source.ip result it should list all the ip address results in the values box that pops up next to it.  We'll click on the plus button at the top of this box.

Let's add the user next.  In the same search box we used for source.ip we'll seach for `user.name`.  The two results that are closed to what we want are winlog.user.name and user.name.  There's no data in the winlog listing but there are values in the user.name so we'll click on the plus sign button ot add it to our columns.  
We can confirm user.name by opening up one of the alerts and looking at the message field which shows "Account Name: ADMINISTRATOR".  And this matches what's on the user.name column along with the IP listed in the source.ip column for the alert we opened.

I even went and added the country field as well so I could see where the attempts are coming from. `source.geo.country_name`.

Finally, we'll save this filter with the blue save button at the top right corner of the page.  A smaller windows should pop up and we'll give it a name of `RDP Failed Activity` and click the blue save button at the bottom of the window.

We can confirm this filter works with RDP by by making a failed attept to log into RDP on your host PC to the MyDFIR Windows VM.  Once completed you should get an alert with your host PC's IP address.

** Create Threshold Rule for both SSH and RDP **

On the same Discover page where we created our RDP filter in the last section we'll click on 'Alerts' at the top right and select "Create search threshold rule".
- We'll give it a name of `MyDFIR-SSH Brute Force Activity-(your handle)`.
- Under "Define your query" verify you have "event.code: 4625" listed.
- Adjust the 'IS ABOVE' field from 1000 to 5.
- We'll test it with the "Test query" button and you if it worked you should get a query matched documents result just below the button.
  eg. "Query matched 24 documents in the last 5m".
- Click the save button at the bottom right.

To view our alerts you can click on the hamburger menu, click on "Stack Management" under Management, and choose "Alerts" under Alerts and Insights.  If you click on the three dots a the beginning of the alert and select "view alert details" we can see the more information about the alert.

As you can see these alerts do not have much info in them.  There is a better way to show the type of info that is in our filters like the Discover panel.

- Click on the Hamburger menu.
- Under Security select "Rules".
- Next choose "Detection rules (SIEM)".
- Click on the blue "Create new rule" button at the top right.
- In the middle under Define rule we'll select "Threshold".
- In the custom query field we can enter `system.auth.ssh.event : * and agent.name: MYDFIR-Linux-(your handle name) and system.auth.ssh.event: Failed and user.name: root`.
- In the Group by field we'll enter `user.name` and `source.ip`.
- In the Required fields we'll add two separate fields with the same items:  `user.name` and `source.ip`.
- Click on the blue "Continue" button.
- For the 2. About rule section we'll call it `MyDFIR-SSH-Brute-Force-Attempt-(Your Handle)`.
- In Description we can enter `This rule detects failed authenticaitons towards the account: root`.
- We'll set the Default severity drop down to "Medium".
- In the Advanced settings we'll add in the "Custom Highlighted fields" with `source.ip`.

There are some nice options in the Advanced settions to halp analysts with investigations like a URL which might show an exmaple of a type of attack with references to tatics from the MITRE Attack Framework.  There are also text fields for a Setup guide and Investigation guide where you can add your own custom documentation depending on on your SOC's rules and processes you follow.

- Scroll to the bottom and click on the blue "Continue" button.
- For the 3. Schedule rule we'll adjust both "Runs every" and "Additional look back time" to 5 minutes.
- Click the blue "Continue button.
- Step 4 can be left as default and we'll click on the blue "Create & enable rule" button.

We'll create one more rule for RDP.  Everything is the same except for the following:
- In the custom query field we can enter `event.code: 4625 and agent.name: MYDFIR-WIN-(Your Handle) and user.name: Adminstrator`.
- For the 2. About rule section we'll call it `MyDFIR-RDP-Brute_Force Atempt-(Your Handle)`.
- In Description we can enter `This rule detects failed authentication towards the account Administrator via RDP`.
- For the 3. Schedule rule we'll adjust "Runs every" to 1 minute and "Additional look back time" to 5 minutes.

Overtime once we start collecting alerts we should see the following layout under the Security -> Alerts section.

![Elastic Security Alerts Layout](https://github.com/user-attachments/assets/78783bd4-cb53-4171-b6b8-d47acfffc41c)

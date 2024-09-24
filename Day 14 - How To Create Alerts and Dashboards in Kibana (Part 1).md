## Day 14 - How To Create Alerts and Dashboards in Kibana (Part 1)
**Objective:  Create SSH Brute Force Alert & Dashboard**


### Alert Creation

To make a brute force alert in your Elastic instance we want to capture a few things first:  Failed attempts, user, and source ip.  After searching for a bit and the help of the Day 14 tutorial, the fields we want to add to our alart are the following:
- system.auth.ssh.event
- user.name
- source.ip

To add a field as a column you just click on the small circled plus sign symbol to the right when hovering over a field name.  We also added "souce.geo.country_name" as well to show the country origin.  Your data may be different but the format should look like the below image.

![Elastic Discover filtered Failed SSH columns](https://github.com/user-attachments/assets/60dd697f-4d02-4ed8-bc0f-65316a4fd3eb)

We can save this query as an alert by click "Alerts" at the top right corner and selecting "Create search threshold rule".  A new window on the right should appear and we'll give our Alert a name "MyDFIR-SSH Brute Force Activity-(Your handle name)".

The one thing we want to change in here is the "IS ABOVE" field from 1000 to 5 so we can see more alerts more often.  The rest can be left as default and we can click on the blue "Save" button.  If there's no actions you might get another window come up which you can just click on the "Save Rule" button.

### Dashboard Creation

The first thing we'll need is to build our search query string from the alert we just made.

`system.auth.ssh.event : * and agent.name: MYDFIR-Linux-(your handle name) and system.auth.ssh.event: Failed`

You can confirm it works by pasting it in the filter field of the discover section as it should show the same data as your alert.  Once that's confirmed we can move into the maps section (Hamburger menu -> Analytics -> Maps) and paste our query string into the search field at the top.  Next we'll select the blue "Add layer" button on the right.

![Elastic Maps Add Layer Menu](https://github.com/user-attachments/assets/3c953f60-d974-4c13-bdd5-dc7e35107c67)

There are a lot of options but the one we want is "Choropleth" which will shade the locations that attempted a Brute Force atack.

The next section we'll leave the Boundary Source as "Administrative boundaries from the Elastic Maps Service".  In the EMS boundaries we'll select "World Countries" and under Statistics source for the "Data view" we'll pick the ".alerts-security.alerts..." option which should be the same data view we're using in the Discover section and verify with the blue drop down underneath the Hamburger menu.

![Elastic Dicover Data view](https://github.com/user-attachments/assets/f2e5a1b8-6736-42f8-8e72-a5afb7c703c1)

And for the "Join field" we'll choose `source.geo.country.iso.code`.  Click on the blue "Add and continue" button at the bottome right.  The rest of the settings can be left as default and we can click on the blue "Save" button at the top right corner.

We'll give our Map a title like "SSH Failed Authentications" and to add this to a dashboard we'll select the "New" option and select the blue "Save and go to Dashboard" button.

Finally to save the dashboard we select the blue "Save" button at the top right corner and in the new window we'll name it "MyDFIR-Authenticaiton-Activity" and clck on the blue "Save" button within that window and we should see the below map with various countries colored in.

![SSH Failed Authentications Map](https://github.com/user-attachments/assets/55bc33c8-4b70-4a27-a5e1-8511fc264f52)

We can even map a successful authentication map by clicking on the 3 dots at the top right corner of your map and selecting duplicate.  And we'll give the new map a title such as "SSH Successful Authentications".  We'll need to modify our query to only look at successful attempts so we'll click on the 'Edit" link in the Query section below.

A new map window should show up with our Failed query string we used in the first map.  By modifiying the query in the discover section we found the key word for a successful attempt is labelled "Accepted" so we'll replace "Failed" at the end of our string to "Accepted".  To confirm it works you can change the timefrme to the last 90 days and you should see at least one shaded in option from your country.  Once you confirm you can click on the blue "Save and return" button at the top right corner.

You can set your timeframe to your liking but as per the tutorial I'll select "Last 15 minutes" and click on the blue "Save" button at the top right corner.  You should now have a dashboard showing world maps of failed and successful SSH brute force attempts by country!

![Elastic dashboard duel map](https://github.com/user-attachments/assets/eae407e6-606c-47d5-bf23-22500d2d10ce)

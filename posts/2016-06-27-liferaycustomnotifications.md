---
title: Creating custom notifications in Liferay
description: Creating custom notifications in Liferay
date: 2016-06-27
tags:
  - Liferay
  - Notifications
---

As I mentioned before, I've recently started working with the [Liferay] platform.  I had some free time and decided I would spend it learning how to add custom notifications to one of the administrative portlets that contained a long-running process.  So, I scoured the internet for any documentation on how to specify custom notifications in Liferay.  What I found was this [article] (and various [re]-[gur]-[gi]-[tations] of the information).  In the article, the author describes the following steps:

* Adding an XML file which defines the delivery types for the notification
* Adding a custom handler to produce the body of the notification
	- This contains a hard-coded portlet ID
* Updating the liferay-portal.xml definition to include references to the custom definitions/handler

I wanted to take a slightly different approach.  Liferay requires the custom handler to return the body of the notification but the XML file detailing the delivery types is not something I wanted to maintain.  So, here is what I did to get custom notifications working without the XML file modifications.

First, I created the custom handler and overrode the GetBody and GetLink methods:

``` java
@Override
protected String getBody(
		UserNotificationEvent userNotificationEvent,
		ServiceContext serviceContext) throws Exception {
	JSONObject jsonObject =
		JSONFactoryUtil.createJSONObject(
			userNotificationEvent.getPayload()
		);
	String notificationTitle=jsonObject.getString("title");
	String notificationDetails = jsonObject.getString("status");
	String body = StringUtil.replace(getBodyTemplate(), new String[] {
		"[$TITLE$]", "[$BODY_TEXT$]" },
		new String[] {
			"<strong>" + notificationTitle + "</strong>",
			notificationDetails });
	return body;
}

@Override
protected String getLink(
	UserNotificationEvent userNotificationEvent,
	ServiceContext serviceContext) throws Exception {
	return super.getLink(userNotificationEvent, serviceContext);
}

protected String getBodyTemplate() throws Exception {
	return "<div class=\"title\">[$TITLE$]</div>" +
				"<div class=\"body\">[$BODY_TEXT$]</div>");
}
```
Next, I wanted to address the hard-coded portlet ID from the [article] so I created a public constructor that accepts a String of the portlet ID and calls the "setPortletId" function.

``` java
public CustomNotificationHandler(String portletId) {
		setPortletId(portletId);
	}
```

Once I had the handler finished, I set out to programmatically create the appropriate definitions...

``` java
CustomNotificationHandler hndlr = new com.example.CustomNotificationHandler(
	themeDisplay.getPortletDisplay().getId());

UserNotificationManagerUtil.addUserNotificationHandler(hndlr);

if(UserNotificationManagerUtil.fetchUserNotificationDefinition(
	hndlr.getPortletId(),
	ClassNameLocalServiceUtil.getClassNameId(hndlr.getClass().getName()),
	UserNotificationDeliveryConstants.TYPE_WEBSITE).equals(null))
{

    UserNotificationDefinition def =
		new UserNotificationDefinition(
			hndlr.getPortletId(),
			ClassNameLocalServiceUtil.getClassNameId(
				hndlr.getClass().getName()
			),
			0,
			"Receive notification when this " +
			"portlet does something INCREDIBLE." );

    def.addUserNotificationDeliveryType(
		new UserNotificationDeliveryType(
	    	"Website",
		    UserNotificationDeliveryConstants.TYPE_WEBSITE,
			true,
			true)
	);

	UserNotificationManagerUtil.addUserNotificationDefinition(
		hndlr.getPortletId(),
		def);
}

```

Finally, I had to create and <strike>send</strike> add the actual message:

``` java
JSONObject payload = JSONFactoryUtil.createJSONObject();
payload.put("status", "This is the extraordinary notification message.");
payload.put("title", "Tie Til");
payload.put("userId", CurrentUser.getUserId());

UserNotificationEvent notification =
	UserNotificationEventLocalServiceUtil.createUserNotificationEvent(
		CounterLocalServiceUtil.increment()
	);

notification.setCompanyId(companyId);
notification.setUserId(CurrentUser.getUserId());
notification.setPayload(payload.toString());
notification.setDeliverBy(UserNotificationDeliveryConstants.TYPE_WEBSITE);
notification.setTimestamp(new Date().getTime());
notification.setArchived(false);
notification.setType(hndlr.getPortletId());

UserNotificationEventLocalServiceUtil.addUserNotificationEvent(notification);
```

[article]: http://www.codeyouneed.com/liferay-custom-notifications/
[Liferay]: https://www.liferay.com/
[re]: http://www.liferaysavvy.com/2014/12/liferay-dockbar-custom-user.html
[gur]: http://stackoverflow.com/questions/33821623/using-liferay-dockbar-notifications
[gi]: http://livewithliferay.blogspot.com/2014/12/custom-notifications-implementation-in.html
[tations]: https://community.liferay.com/forums/-/message_boards/message/55556877#_com_liferay_message_boards_web_portlet_MBPortlet_message_34854584

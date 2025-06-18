# HarmonyOS Next agent reminds development: Create an intelligent notification system with "zero leaks"

hello!I am Xiao L, the female programmer who "makes reminders more warm" in the Hongmeng notification field~ Do you know?Correct proxy reminder design can increase user retention rate by 25%!Today, let’s reveal the “precise access law” of Hongmeng agent reminder – the four core capabilities of ** type selection, interaction design, permission management, and cross-device synchronization, so that your reminder is both "just right" and "warm and caring"!


## 1. The "three-dimensional coordinate system" reminder of agents
### (I) Type selection matrix
| Scene | Countdown Reminder | Calendar Reminder | Alarm Reminder | Combination Strategy |
|---------------------|---------------------|---------------------|---------------------|------------------------|
| Conference Countdown (10 minutes) | ● | | | Single Trigger + Strong Reminder |
| Monthly Bill (1st of each month) | | ● | | Monthly Loop + Silent Notification |
| Drink water every day (9:00-18:00) | | | | ● | Weekly cycle + multi-device synchronization |
| New Year's Eve Event (2024.12.31) | ● | ● | | Date Trigger + Countdown Warm-up |

### (II) ReminderRequest core parameters
```typescript
// Basic reminder parameters (general)
interface BaseReminder {
reminderType: ReminderType; // Type (required)
title: string; // Title (required, ≤30 words)
content: string; // Content (required, ≤100 words)
notificationId: number; // Unique notification ID (required)
slotType: SlotType; // Notification channel (required, such as social/system/warning)
wantAgent: WantAgent; // Click to notify and jump to the target (required)
}

// Countdown reminder parameters
interface TimerReminder extends BaseReminder {
    reminderType: ReminderType.REMINDER_TYPE_TIMER;
triggerTimeInSeconds: number; // Trigger time (seconds, ≤30 days)
expiredContent?: string; // Expired document
}

// Calendar reminder parameters
interface CalendarReminder extends BaseReminder {
    reminderType: ReminderType.REMINDER_TYPE_CALENDAR;
dateTime: DateTime; // Specific date and time (year/month/day/hour/minute/second)
repeatMonths?: number[]; // Repeat month (1-12, such as [1,3,5])
repeatDays?: number[]; // Repeat date (1-31)
}

// Alarm clock reminder parameters
interface AlarmReminder extends BaseReminder {
    reminderType: ReminderType.REMINDER_TYPE_ALARM;
hour: number; // Hours (0-23)
minute: number; // Minutes (0-59)
daysOfWeek?: number[]; // Repeat the week (1-7, 1=Monday)
snoozeTimes?: number; // Number of snooze times (default 0)
timeInterval?: number; // Snooze interval (seconds, default 300)
}
```


## 2. Interaction design: Let reminder "understand the scene and be warm"
### (I) Notification style optimization
#### 1. Rich Media Notification
```typescript
const reminder: CalendarReminder = {
// ...Basic Parameters
largeIcon: 'icon_event.png', // Big icon (192x192px)
smallIcon: 'icon_event_small.png', // Small icon (48x48px)
progress: { // Progress bar (such as task completion)
        current: 60,
        total: 100,
        show: true
    },
actions: [ // Interactive button
        {
            type: ActionButtonType.ACTION_BUTTON_TYPE_DEEP_LINK,
title: 'View details',
            deepLink: 'app://event/detail/123'
        },
        {
            type: ActionButtonType.ACTION_BUTTON_TYPE_SNOOZE,
title: 'Reminder later',
snoozeTime: 30 * 60 // Try again after 30 minutes
        }
    ]
};
```

#### 2. Multilingual Adaptation
```typescript
// Automatically switch document according to system language
const lang = ohos.i18n.getSystemLanguage();
const reminderContent = {
'zh-CN': 'The meeting is about to begin',
    'en-US': 'Meeting starts soon',
'ja-JP': 'Conference start'
}[lang] || 'Reminder';
```

### (II) Intelligent and free from disturbance
```typescript
// Detect user status (such as driving/sleep) and automatically adjust the reminder method
const userStatus = deviceManager.getUserStatus();
if (userStatus === UserStatus.DRIVING) {
reminder.slotType = SlotType.SYSTEM_ALERT; // Drive mode strong reminder
reminder.ringDuration = 10; // Extend ringing time
} else if (userStatus === UserStatus.SLEEPING) {
reminder.slotType = SlotType.QUIET_NOTIFICATION; // Silent notification
reminder.vibrationPattern = []; // Turn off vibration
}
```


## 3. Cross-device reminder: Build a "seamless reach" network
### (I) Multi-terminal synchronization strategy
```typescript
// Synchronize to other devices when the main device is created
function publishCrossDeviceReminder(reminder: BaseReminder, devices: string[]) {
    devices.forEach(deviceId => {
        const crossReminder = {
            ...reminder,
targetDeviceId: deviceId, // Target device ID
syncType: SyncType.SYNC_TYPE_REALTIME // Real-time synchronization
        };
        reminderAgentManager.publishReminderToDevice(crossReminder);
    });
}

// Scene: Create reminder on your phone, sync to your watch/tablet
publishCrossDeviceReminder(timerReminder, ['watch-123', 'pad-456']);
```

### (II) Equipment capability adaptation
```typescript
// Adjust the reminder form according to the type of equipment
const deviceInfo = deviceManager.getDeviceInfo();
if (deviceInfo.type === DeviceType.WATCH) {
reminder.content = reminder.content.slice(0, 50) + '...'; // Watch limit is 50 words
reminder.actions = [{ type: ActionButtonType.ACTION_BUTTON_TYPE_CLOSE }]; // Only the close button is retained
} else if (deviceInfo.type === DeviceType.TV) {
reminder.showOnLockScreen = true; // TV lock screen display
reminder.priority = NotificationPriority.HIGH; // High priority
}
```


## 4. Permissions and Compliance: The bottom line thinking of "safety first"
### (I) The entire process of authorization application
1. **Configuration file declaration**:
   ```json
   "reqPermissions": [
       {
           "name": "ohos.permission.PUBLISH_AGENT_REMINDER",
"reason": "Conference reminder is required"
       }
   ]
   ```

2. **User Authorization Boot**:
   ```typescript
   function requestReminderPermission() {
       permission.requestPermissionsFromUser([Permission.PUBLISH_AGENT_REMINDER])
           .then((result) => {
               if (result[0].granted) {
publishReminder(); // Reminder after authorization
               } else {
showPermissionGuide(); // Guide the user to the settings page to authorize
               }
           });
   }
   ```

3. **Huawei official application (if required)**:
- Email Subject: `[Agent Reminder Permission Application] Application Name-Package Name`
- Required information:
     ```
Application scenario: Daily health check-in reminder, frequency 1 time/day, notification title: "It's time to check in", notification content: "Today's exercise goal is 50% completed"
     ```

### (II) Privacy protection practices
1. **Sensitive information encryption**:
   ```typescript
// Encrypted transmission when the reminder content contains sensitive data
   const encryptedContent = crypto.encrypt(reminder.content, secretKey);
   reminder.content = encryptedContent;
// Decryption on trigger (in ExtensionAbility)
   const decryptedContent = crypto.decrypt(encryptedContent, secretKey);
   ```

2. **User Data anonymization**:
Avoid including user privacy information (such as contact information, address) in reminders, and use desensitization treatment if you need to display it.


## 5. Practical cases: "Intelligent Schedule Management" full scenario implementation
### Scene description:
Users create meeting reminders on their phones, automatically sync to their watches, and trigger progressive reminders 1 hour or 10 minutes before the meeting begins

### Implementation steps:
#### 1. Create multi-level reminder
```typescript
// Main reminder (at the beginning of the meeting)
const mainReminder: CalendarReminder = {
    reminderType: ReminderType.REMINDER_TYPE_CALENDAR,
    dateTime: { year: 2024, month: 5, day: 20, hour: 14, minute: 0 },
// ...Other parameters
};

// Pre-reminder (1 hour in advance)
const preReminder: TimerReminder = {
    reminderType: ReminderType.REMINDER_TYPE_TIMER,
triggerTimeInSeconds: 3600, // 1 hour in advance
// ...Other parameters, content is "The meeting will start after 1 hour"
};
```

#### 2. Multi-device synchronization
```typescript
const devices = await deviceManager.getPairedDevices(DeviceType.WATCH);
devices.forEach(deviceId => {
// Watch reminder simplifies content
    const watchReminder = {
        ...mainReminder,
        content: mainReminder.content.slice(0, 30) + '...',
        targetDeviceId: deviceId
    };
    reminderAgentManager.publishReminderToDevice(watchReminder);
});
```

#### 3. Snooze logic
```typescript
// Handle user clicks the snooze button
reminderAgentManager.on('snooze', (reminderId) => {
const snoozeTime = 10 * 60; // Try again after 10 minutes
    reminderAgentManager.scheduleSnooze(reminderId, snoozeTime);
});
```


## 6. Pit avoidance guide: "Red line list" for agent reminders
### (I) System restrictions and avoidance
1. **Frequency Limit**:
The same application sends up to 10 reminders per minute, and up to 100 reminders per day (can be queried through `getReminderQuota`)

2. **Content Specification**:
- Title/content is prohibited from containing sensitive words (such as advertising, political terms)
- Notification icons must comply with the "HarmonyOS Design Specifications", and dynamic icons are prohibited

3. **User Experience**:
- Avoid sending strong reminders between 22:00-08:00 (unless urgent)
- Provide the option of "No reminder anymore" to allow users to permanently close certain types of reminders

### (II) Exception scene processing
1. **Permission denied**:
   ```typescript
   if (!permission.hasPermission(Permission.PUBLISH_AGENT_REMINDER)) {
router.pushUrl('app://settings/reminder-permission'); // Jump the permission settings page
       return;
   }
   ```

2. **Device offline**:
   ```typescript
// It is automatically cached when sent to offline devices and re-send after online
   reminderAgentManager.enableOfflineDelivery(reminderId, true);
   ```

3. **Repeat reminder**:
   ```typescript
// Deduplication by day (on the same date, same type reminder will only be kept latest)
   reminder.replaceMode = ReminderReplaceMode.REPLACE_MODE_KEEP_LATEST;
   ```


## 7. Cold knowledge: "Future evolution" reminded by agents
### (I) AI intelligent reminder
- Predict the reminder timing based on user behavior (such as commuting time automatically triggers road conditions reminder)
- Natural language analysis creation reminder: "Remind me to have a meeting at 10 am tomorrow"`

### (II) Cross-application reminder aggregation
```typescript
// Merge reminders from the same developer (such as promotional notifications for e-commerce applications)
reminder.groupKey = 'com.example.shopping';
```

### (III) Environmental perception reminder
```typescript
// Trigger coupon reminder when a user enters the mall is detected
locationManager.on('enterGeofence', (geofenceId) => {
    if (geofenceId === 'mall-123') {
        publishReminder(shopCouponReminder);
    }
});
```


## Last reminder: The "temperature formula" of agent reminder
**User satisfaction = Reminder accuracy × Interaction friendly ÷ Interference frequency**
- Accuracy: Three-dimensional trigger based on time, place, and equipment
- Friendliness: clear information level + convenient operation path
- Interrupt frequency: Respect user routines and provide flexible setting options

Want to know how to use Hongmeng to implement "User Behavior Analysis of Reminders and Notifications"?Follow me and you will unlock new skills next time!If you think the article is useful, share it with the product manager in the team. Let us remind you "valued, don't disturb"!😉

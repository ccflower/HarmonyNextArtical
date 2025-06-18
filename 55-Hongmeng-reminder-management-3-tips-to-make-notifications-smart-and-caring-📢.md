
# Hongmeng reminder management: 3 tips to make notifications "smart and caring" 📢

Hi~ I am Xiao L!In Hongmeng development, the reminder function is like a "care little secretary" - it can trigger notifications accurately in the background, and avoid power consumption.Today, we will take you to unlock the core gameplay of proxy reminders to make your application notifications smarter!


## 1. Agent reminder: "Guardian" of background notification🔒
### Core Advantages
- **Process irrelevant**: Even if the application is killed, the system will trigger reminders on time
- **Low consumption and high efficiency**: Unified scheduling by the system to avoid applications resident in the backend
- ** Rich scenes**: Supports three types of countdown, calendar, and alarm clock

### Typical application scenarios
| Type | Scenario Examples | Core Parameters |
|--------------|-----------------------------------|---------------------------|  
| Countdown reminder | 10 minutes before the meeting begins | `triggerTimeInSeconds: 600` |
| Calendar reminder | Reminder credit card repayment on the 1st of each month | `dateTime: {month: 1, day: 1}` |
| Alarm clock reminder | Alarm clock at 7 am on weekdays | `hour: 7, daysOfWeek: [1,2,3,4,5]` |


## 2. Three steps for development: from permissions to release🚀
### 1. Apply for permissions (key first step)
```json  
// config.json  
{  
  "reqPermissions": [  
    { "name": "ohos.permission.PUBLISH_AGENT_REMINDER" }  
  ]  
}  
```  
**Note**: You need to apply for official permissions from Huawei by email (temporary permissions can be used in the testing phase)

### 2. Define reminder content (taking calendar reminder as an example)
```typescript  
import { ReminderType, ReminderRequestCalendar } from '@ohos.reminderAgentManager';  

const birthdayReminder: ReminderRequestCalendar = {  
  reminderType: ReminderType.REMINDER_TYPE_CALENDAR,  
  dateTime: {  
    year: 2024,  
    month: 5,  
    day: 20,  
    hour: 9,  
    minute: 0  
  },  
repeatMonths: [5], // Repeat in May every year
  actionButtons: [  
{ title: 'View Gift', type: 'BUTTON_TYPE_ACTION' },
{ title: 'Delay reminder', type: 'BUTTON_TYPE_SNOOZE' }
  ],  
title: 'Friends birthday reminder',
content: 'Today is Xiao Ming's birthday, remember to send blessings~',
notificationId: 1001, // Unique notification ID
slotType: 'SOCIAL_COMMUNICATION' // Notification channel type
};  
```  

### 3. Release and management reminders
```typescript  
// 发布提醒  
function scheduleReminder(reminder: ReminderRequestBase) {  
  reminderAgentManager.publishReminder(reminder).then(reminderId => {  
    console.log(`提醒已发布，ID：${reminderId}`);  
    saveReminderId(reminderId); // 本地存储ID以便后续管理  
  });  
}  

// 取消提醒  
function cancelReminder(reminderId: number) {  
  reminderAgentManager.cancelReminder(reminderId).then(() => {  
    console.log('提醒已取消');  
  });  
}  
```  


## 三、三种提醒类型对比表📊  
| **维度**       | **倒计时提醒**      | **日历提醒**        | **闹钟提醒**        |  
|----------------|---------------------|---------------------|---------------------|  
| **触发方式**   | 相对时间（秒）      | 绝对日期时间        | 每日固定时间        |  
| **重复支持**   | 不支持              | 按日/月重复         | 按周重复            |  
| **典型场景**   | 考试倒计时、烹饪计时| 会议预约、节日提醒  | 起床闹钟、吃药提醒  |  
| **核心参数**   | `triggerTimeInSeconds` | `dateTime`/`repeatMonths` | `hour`/`daysOfWeek` |  


## 四、通知优化：让提醒更「顺眼」👀  
### 1. 自定义通知渠道（NotificationSlot）  
```typescript  
import { NotificationSlot, SlotType, Importance } from '@ohos.notificationManager';  

const reminderSlot: NotificationSlot = {  
  slotId: 'reminder_channel',  
  slotType: SlotType.SYSTEM_PROMPT,  
  name: '重要提醒',  
  importance: Importance.HIGH, // 高优先级（弹窗提醒）  
  description: '用于显示关键业务提醒',  
  enableVibration: true,  
  ledColor: '#FF0000' // 呼吸灯红色  
};  

// 创建渠道  
notificationManager.addNotificationSlot(reminderSlot).then(() => {  
  console.log('通知渠道创建成功');  
});  
```  

### 2. 智能交互按钮  
```typescript  
// 添加「立即处理」和「稍后提醒」按钮  
const actionButtons = [  
  {  
    title: '立即处理',  
    type: 'BUTTON_TYPE_ACTION',  
want: { // Click the button to jump to the target
      bundleName: 'com.example.app',  
      abilityName: 'TaskDetailAbility',  
      parameters: { taskId: '123' }  
    }  
  },  
  {  
title: 'Reminder after 1 hour',
    type: 'BUTTON_TYPE_SNOOZE',  
snoozeTime: 3600 // 1 hour delay
  }  
];  
```  

### 3. Expiration reminder processing
```typescript  
// Set expired content (untriggered reminder display)
const reminder = {  
  ...baseReminder,  
expiredContent: 'Your meeting reminder has expired, you can view history',
// After expiration, it will automatically jump to the history page
  expiredWant: {  
    bundleName: 'com.example.app',  
    abilityName: 'ReminderHistoryAbility'  
  }  
};  
```  


## 5. Key points for permission application and compliance ⚠️
### 1. Official application process
1. Email to `hwpush@huawei.com`, topic: `[Agent Reminder Permission Application] Application Name - Package Name`
2. The text must include:
- Application scenarios (such as "Medical App Drug Reminder")
- Notification frequency (such as "up to 3 times a day")
- Example of notification content (such as "Your physical examination report has been generated")

### 2. User privacy protection
- Prohibit push notifications that are not related to application features (such as advertisements)
- Provide an entry to close reminder with one click
- Encrypted transmission of sensitive information (such as medical record number in medical reminder)


## 6. Practical scenario: Smart reminder solution for fitness app🏋️
### Scene Description
- User sets fitness reminder every Monday, Wednesday and Friday at 19:00
- 30 minutes to prepare sports equipment
- When the reminder is not responded, it will be automatically delayed after 1 hour

### Implementation logic
1. **Alarm clock reminder**:
   ```typescript  
   const workoutAlarm: ReminderRequestAlarm = {  
     reminderType: 'ALARM',  
     hour: 19,  
     minute: 0,  
daysOfWeek: [1,3,5], // Monday, Wednesday, Friday (1 represents Monday)
snoozeTimes: 2, // 2 delays up to 2 times
timeInterval: 3600, // 1 hour delay each time
     ...commonReminderConfig  
   };  
   ```  

2. **Countdown Reminder**:
   ```typescript  
// Automatically generate countdown 30 minutes before alarm trigger
   function addPreReminder(alarmId: number) {  
     const preReminder: ReminderRequestTimer = {  
       reminderType: 'TIMER',  
triggerTimeInSeconds: 1800, // 30 minutes
linkedReminderId: alarmId, // Related alarm clock reminder ID
       ...commonReminderConfig  
     };  
     scheduleReminder(preReminder);  
   }  
   ```  


## Summary: Remind the design of "Four Elements"
1. ** Accurate timing**: Use countdown/calendar/alarm clock to match the scene time characteristics
2. **Interactive friendly**: Provide clear operation buttons to reduce user decision-making costs
3. **Visual Layering**: Distinguish importance through notification channel settings
4. **Compliance and Security**: Apply for permission + protect privacy to avoid users’ disgust

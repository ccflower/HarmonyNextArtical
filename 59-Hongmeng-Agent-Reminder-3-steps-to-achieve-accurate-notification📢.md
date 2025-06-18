
# Hongmeng Agent Reminder: 3 steps to achieve accurate notification📢

Hi~ I am Xiao L!Hongmeng Agent reminder can still trigger notifications on time after the application is closed.Today, we use 3 core points to quickly master the development skills of three types of reminders: countdown, calendar, and alarm clock~


## Reminders for 1 and 3 categories: Select "Time Trigger" as needed⏰
| Type | Trigger method | Typical scenario | Key parameters |
|------------|----------------|-------------------------|---------------------------|  
| Countdown reminder | Relative time (seconds) | Conference countdown, flash sale reminder | `triggerTimeInSeconds` |
| Calendar Reminder | Absolute Date Time | Birthday, Repayment Reminder | `dateTime`/`repeatMonths` |
| Alarm clock reminder | fixed daily time | Alarm clock and medication reminder | `hour`/`daysOfWeek` |

**Code Example: Countdown Reminder**
```typescript  
const timerReminder = {  
  reminderType: 'TIMER',  
triggerTimeInSeconds: 300, // Remind in 5 minutes
actionButton: [{ title: 'View', type: 'CLOSE' }]
};  
reminderAgentManager.publishReminder(timerReminder);  
```  


## 2. Development process: Permissions → Configuration → Release "Three-step" 🚀
### 1. Application permission (must add!)
```json  
// module.json5  
{  
  "reqPermissions": [  
    { "name": "ohos.permission.PUBLISH_AGENT_REMINDER" }  
  ]  
}  
```  

### 2. Configure reminder content
```typescript  
const alarmReminder = {  
  reminderType: 'ALARM',  
  hour: 7,  
  minute: 30,  
daysOfWeek: [1,2,3,4,5], // Working Day
snoozeTime: 600, // Sleep for 10 minutes
};  
```  

### 3. Publishing and Management
```typescript  
// Post a reminder
reminderAgentManager.publishReminder(alarmReminder).then(id => {  
console.log('Reminder ID:', id);
});  

// Cancel the reminder
reminderAgentManager.cancelReminder(id);  
```  


## 3. Notification optimization: Make reminders more "conspicuous" 👀
### 1. Custom notification channels
```typescript  
const slot = {  
  slotId: 'urgent',  
name: 'Important reminder',
importance: 'HIGH', // pop-up window + ringing
  ledColor: '#FF0000'  
};  
notificationManager.addNotificationSlot(slot);  
```  

### 2. Interactive Buttons
```typescript  
const buttons = [  
{ title: 'processing', type: 'ACTION', want: { abilityName: 'TaskAbility' } },
{ title: 'Delay', type: 'SNOOZE', snoozeTime: 3600 }
];  
```  


## 4. Authorization application: Email template quick lookup table📧
**Topic**: [Agent Reminder Permission Application]-Application Name-Bank Name
**Basic points**:
- Scenario: For example, "Medical App Message Reminder, 3 times a day"
- Frequency: Committed to "single user ≤ 5 items per day"
- Close the entrance: Instructions "Settings page can be closed with one click"

**Example**:
> Apply for daily exercise reminder for "Fitness App". Users can set it 1-3 times a week, click the notification to jump to the training page directly.


## Summary: Remind the design of the "three musts"
- **Be accurate**: Select the right type and use `daysOfWeek` and other parameters to narrow the trigger range
- **Restraint**: Avoid frequent pushing and automatically pause when the battery is low
- **To interact**: Provide the "processing/delay" button to increase user participation

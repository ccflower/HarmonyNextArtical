
# Hongmeng Intelligent Reminder Assistant: 3 steps to realize multi-scene notification📱

Based on Hongmeng agent reminder and delayed tasks, a low-cost and stable reminder system is created. The core logic is as follows:


## 1. Three major reminder types: trigger on demand⏰
| Type | Core Scenario | Key Parameters | Code Sample Snippet |
|------------|-------------------------|---------------------------|-------------------------------|  
| Countdown reminder | Conference countdown, cooking timing | `triggerTimeInSeconds` | `publishReminder({type:'TIMER', trigger:300})` |
| Calendar reminder | Birthday, repayment, meeting appointment | `dateTime`/`repeatMonths` | `dateTime:{year:2024, month:12, day:31}` |
| Alarm clock reminder | Daily wake-up reminder | `hour`/`daysOfWeek` | `daysOfWeek:[1,2,3,4,5]` (working days) |

**Unified Publishing Interface**
```typescript  
function scheduleReminder(reminder: ReminderRequestBase) {  
  reminderAgentManager.publishReminder(reminder).then(id => {  
console.log('Reminder ID:', id); // Storage ID is used for management
  });  
}  
```  


## 2. Backstage task optimization: low consumption and stability
### 1. System grouping scheduling policy
| Application activity level | Minimum reminder interval | Applicable scenarios |
|----------------|--------------|------------------------|  
| Active (such as social) | 2 hours | Instant Message |
| Commonly used (such as tools) | 24 hours | Regular data synchronization |
| Rarely used | 48 hours | Low frequency cleaning reminder |

### 2. Dynamic resource adaptation
- **Low battery mode**: Pause non-emergency reminder when power is <15%.
  ```typescript  
  batteryManager.on('levelChanged', (level) => {  
    if (level < 15) cancelAllReminders();  
  });  
  ```  
- **When memory is insufficient**: Priority reserves high priority reminders (such as alarm clock)
  ```typescript  
  const highPriorityReminder = { priority: 'HIGH', overridePolicy: true };  
  ```  


## 3. Notification experience optimization: Interaction and beauty are both important✨
### 1. Custom notification channels
```typescript  
const reminderSlot = {  
  slotId: 'alarm_channel',  
name: 'Important reminder',
importance: 'HIGH', // pop-up window + ringing + breathing light
vibrationPattern: [100, 200], // Vibration rhythm (milliseconds)
  ledColor: '#FF5733'  
};  
notificationManager.addNotificationSlot(reminderSlot);  
```  

### 2. Interactive button design
```typescript  
const buttons = [  
  {  
title: 'Train now',
    type: 'ACTION',  
want: { abilityName: 'TaskDetailAbility' } // Click to jump
  },  
  {  
title: 'Reminder after 1 hour',
    type: 'SNOOZE',  
snoozeTime: 3600 // Delay execution
  }  
];  
```  


## 4. Authorization application and compliance📧
**Email Template Shorthand**:
- Topic: [Agent Reminder Permission]-Application Name-Brand Name
- Key points of the text:
✅ Scenario: For example, "Medical App Reminder 3 times a day"
✅ Frequency: Committed to "single user ≤5 items per day"
✅ Close the entrance: "Settings page provides one-click close"

**Example**:
> Apply for daily exercise reminder for "Fitness Assistant". Users can set 3 times a week and click notification to go directly to the training plan.


## Summary: Core Competency Map
```mermaid  
graph LR  
A[User Setting Reminder] --> B{Type Judgment}
B -->|Countdown| C[TIMER Type Agent Reminder]
B -->|Calendar Events|D[CALENDAR Type Agent Reminder]
B -->|Daily Alarm Clock|E[ALARM Type Agent Reminder]
CDE --> F[System Group Scheduling]
F --> G[Dynamic Resource Adaptation]
G --> H[Notification Channel Display]

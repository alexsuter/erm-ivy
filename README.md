# erm-ivy


erDiagram
    IWA_SECURITYSYSTEM {
        bigint SecuritySystemId PK
        varchar Name
        bigint SecurityDescriptorId FK
    }
    IWA_APPLICATION {
        bigint ApplicationId PK
        varchar Name
        int Version
        bigint SecuritySystemId FK
    }
    IWA_PROJECT {
        bigint ProjectId PK
        bigint ApplicationId FK
        varchar Name
    }

    IWA_SECURITYMEMBER {
        varchar SecurityMemberId PK
        varchar Name
        bigint SecuritySystemId FK
        int Type
    }
    IWA_ROLE {
        varchar SecurityMemberId PK,FK
        bigint RoleId
        varchar ParentSecurityMemberId FK
        bigint SecuritySystemId FK
    }
    IWA_USER {
        varchar SecurityMemberId PK,FK
        bigint UserId
        bigint SecuritySystemId FK
        varchar Name
    }
    IWA_USERROLE {
        varchar RoleSecurityMemberId PK,FK
        varchar UserSecurityMemberId PK,FK
    }

    IWA_TASKELEMENT {
        bigint TaskElementId PK
        bigint ProjectId FK
        varchar ProcessElementId
    }
    IWA_TASKSTART {
        bigint TaskStartId PK
        bigint TaskElementId FK
    }
    IWA_TASKEND {
        bigint TaskEndId PK
        bigint TaskElementId FK
    }

    IWA_CASE {
        bigint CaseId PK
        varchar UUID
        bigint BusinessCaseId FK
        bigint SecuritySystemId FK
        bigint ApplicationId FK
        bigint TaskStartId FK
        varchar CreatorId FK
        bigint CreatorTaskId FK
    }
    IWA_TASKSWITCHEVENT {
        bigint TaskSwitchEventId PK
        bigint TaskElementId FK
        bigint CaseId FK
    }
    IWA_TASK {
        bigint TaskId PK
        varchar UUID
        bigint CaseId FK
        bigint BusinessCaseId FK
        bigint ApplicationId FK
        bigint SecuritySystemId FK
        bigint TaskStartId FK
        bigint TaskEndId FK
        bigint StartTaskSwitchEventId FK
        bigint EndTaskSwitchEventId FK
        varchar WorkerId FK
    }

    IWA_CASEOWNER {
        bigint CaseId PK,FK
        varchar OwnerId PK,FK
    }
    IWA_CASELOCALIZED {
        bigint CaseLocalizedId PK
        bigint CaseId FK
        bigint LanguageId FK
    }
    IWA_TASKLOCALIZED {
        bigint TaskLocalizedId PK
        bigint TaskId FK
        bigint LanguageId FK
    }
    IWA_LANGUAGE {
        bigint LanguageId PK
        bigint SecuritySystemId FK
        varchar Locale
    }

    IWA_WORKFLOWEVENT {
        bigint WorkflowEventId PK
        bigint ApplicationId FK
        bigint CaseId FK
        bigint TaskId FK
        varchar UserId FK
    }
    IWA_INTERMEDIATEEVENT {
        bigint IntermediateEventId PK
        bigint TaskStartId FK
        bigint ApplicationId FK
        bigint TaskId FK
        bigint SecuritySystemId FK
    }

    IWA_NOTIFICATION {
        varchar NotificationId PK
        bigint SecuritySystemId FK
        bigint ProjectId FK
    }
    IWA_NOTIFICATIONRECEIVER {
        varchar NotificationId PK,FK
        varchar ReceiverId PK,FK
    }
    IWA_NOTIFICATIONDELIVERY {
        varchar NotificationDeliveryId PK
        varchar NotificationId FK
        varchar ReceiverId FK
    }
    IWA_NOTIFICATIONSUBSCRIPTION {
        bigint NotificationSubscriptionId PK
        varchar SubscriberId FK
        varchar Kind
        varchar Channel
    }

    IWA_BLOB {
        varchar UUID PK
        bigint BlobId
        bigint ApplicationId FK
        bigint SecuritySystemId FK
        varchar CreatorId FK
        varchar ModifierId FK
    }

    IWA_SECURITYSYSTEM ||--o{ IWA_APPLICATION : has
    IWA_APPLICATION ||--o{ IWA_PROJECT : contains

    IWA_SECURITYSYSTEM ||--o{ IWA_SECURITYMEMBER : owns
    IWA_SECURITYMEMBER ||--|| IWA_ROLE : specializes
    IWA_SECURITYMEMBER ||--|| IWA_USER : specializes
    IWA_ROLE ||--o{ IWA_USERROLE : role_assignment
    IWA_USER ||--o{ IWA_USERROLE : user_assignment

    IWA_PROJECT ||--o{ IWA_TASKELEMENT : defines
    IWA_TASKELEMENT ||--o{ IWA_TASKSTART : has
    IWA_TASKELEMENT ||--o{ IWA_TASKEND : has

    IWA_APPLICATION ||--o{ IWA_CASE : scopes
    IWA_SECURITYSYSTEM ||--o{ IWA_CASE : sec_scope
    IWA_TASKSTART ||--o{ IWA_CASE : starts
    IWA_SECURITYMEMBER ||--o{ IWA_CASE : created_by

    IWA_CASE ||--o{ IWA_TASK : contains
    IWA_CASE ||--o{ IWA_TASKSWITCHEVENT : switches
    IWA_TASKELEMENT ||--o{ IWA_TASKSWITCHEVENT : emits

    IWA_TASKSTART ||--o{ IWA_TASK : via_start
    IWA_TASKEND ||--o{ IWA_TASK : via_end
    IWA_TASKSWITCHEVENT ||--o{ IWA_TASK : start_event
    IWA_TASKSWITCHEVENT ||--o{ IWA_TASK : end_event
    IWA_SECURITYMEMBER ||--o{ IWA_TASK : worked_by

    IWA_CASE ||--o{ IWA_CASEOWNER : ownership
    IWA_SECURITYMEMBER ||--o{ IWA_CASEOWNER : owner

    IWA_SECURITYSYSTEM ||--o{ IWA_LANGUAGE : provides
    IWA_LANGUAGE ||--o{ IWA_CASELOCALIZED : localizes
    IWA_CASE ||--o{ IWA_CASELOCALIZED : localized
    IWA_LANGUAGE ||--o{ IWA_TASKLOCALIZED : localizes
    IWA_TASK ||--o{ IWA_TASKLOCALIZED : localized

    IWA_APPLICATION ||--o{ IWA_WORKFLOWEVENT : logs
    IWA_CASE ||--o{ IWA_WORKFLOWEVENT : logs
    IWA_TASK ||--o{ IWA_WORKFLOWEVENT : logs
    IWA_SECURITYMEMBER ||--o{ IWA_WORKFLOWEVENT : actor

    IWA_APPLICATION ||--o{ IWA_INTERMEDIATEEVENT : has
    IWA_TASKSTART ||--o{ IWA_INTERMEDIATEEVENT : waits_on
    IWA_TASK ||--o{ IWA_INTERMEDIATEEVENT : source_task
    IWA_SECURITYSYSTEM ||--o{ IWA_INTERMEDIATEEVENT : sec_scope

    IWA_SECURITYSYSTEM ||--o{ IWA_NOTIFICATION : publishes
    IWA_PROJECT ||--o{ IWA_NOTIFICATION : contextual
    IWA_NOTIFICATION ||--o{ IWA_NOTIFICATIONRECEIVER : targets
    IWA_SECURITYMEMBER ||--o{ IWA_NOTIFICATIONRECEIVER : receiver
    IWA_NOTIFICATION ||--o{ IWA_NOTIFICATIONDELIVERY : delivers
    IWA_SECURITYMEMBER ||--o{ IWA_NOTIFICATIONDELIVERY : receiver
    IWA_SECURITYMEMBER ||--o{ IWA_NOTIFICATIONSUBSCRIPTION : subscribes

    IWA_APPLICATION ||--o{ IWA_BLOB : stores
    IWA_SECURITYSYSTEM ||--o{ IWA_BLOB : sec_scope
    IWA_SECURITYMEMBER ||--o{ IWA_BLOB : created_by
    IWA_SECURITYMEMBER ||--o{ IWA_BLOB : modified_by

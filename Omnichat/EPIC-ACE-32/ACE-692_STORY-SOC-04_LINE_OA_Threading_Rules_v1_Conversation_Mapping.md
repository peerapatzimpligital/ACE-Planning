# ACE-692 STORY-SOC-04: LINE OA Threading Rules v1 Conversation Mapping

> **Status:** `TO DO` &nbsp; | &nbsp; **Assignees:** `Unassigned`

## 📝 Description
As a Platform Engineer, I want basic LINE conversation threading rules so that inbound and outbound LINE messages map to the correct conversation during pilot.

**Detail / Description:**
Threading คือกติกาเอาข้อความไปใส่ห้องแชทให้ถูก ใน LINE บางครั้ง mapping อิง user id และ channel account เป็นหลัก ต้องทำให้ deterministic Story นี้กำหนด rule v1 ที่ simple และทดสอบได้ เช่น conversation key based on `tenant_id` + `channel_account_id` + `external_user_id` หรือ optional `thread_id` ก็ได้

**Scope of this story:**
1. Define threading key strategy for LINE v1.
2. Apply rule in normalization pipeline for inbound and outbound mapping.
3. Ensure conversation reuse for repeated messages from same user.
4. Document known limitations such as group chats if not supported.
5. Not include advanced merge contact identity across channels.

**Acceptance Criteria:**
1. **Deterministic conversation key for inbound messages:** Given inbound LINE messages from the same `external_user_id` under the same channel account, when multiple messages arrive, then they map to the same `conversation_id` consistently.
2. **New conversation created when user changes or account differs:** Given messages come from different `external_user_id` or different `channel_account_id`, when processed, then separate conversations are created and not mixed.
3. **Outbound messages map to the same conversation:** Given an outbound LINE send is executed for a conversation, when the message is persisted, then it maps to the same `conversation_id` and does not create a new conversation.
4. **Handling missing fields gracefully:** Given an event lacks optional fields, when threading executes, then it still maps using fallback keys without throwing unhandled errors.

---

## 📋 Custom Fields
| Field | Value |
|---|---|
| Product | Omni |

## 🏗️ Subtasks
| Subtask | Status |
|---|---|
| None | None |

## 🔧 Technical Requirements
| Requirement | Needed? |
|---|---|
| Sequence Diagram | ❌ |
| ER Diagram | ✅ |
| API Spec | ✅ |

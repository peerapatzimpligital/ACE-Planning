# ความสัมพันธ์ (Dependencies) ของ ACE-689 กับ EPIC-ACE-30 และ EPIC-ACE-31

จากการตรวจสอบรายละเอียดของ ACE-689 (EPIC-ACE-32) Story นี้เป็นส่วนเชื่อมต่อ (Adapter) เพื่อรับข้อมูล (Ingestion) และบันทึกลงฐานข้อมูล ซึ่งจำเป็นต้องพึ่งพางานพื้นฐานที่ถูกทำไว้ใน ACE-30 (หมวด Foundation) และ ACE-31 (หมวด Normalization) ดังนี้:

### 1. Dependency กับ EPIC-ACE-30 (FND - Foundation)
- **พึ่งพา FND-01 (Create Tenant, ChannelAccount Model):** ACE-689 มี Acceptance Criteria ว่าต้องแปลกค่า `tenant_id` และ `channel_account_id` ได้ ดังนั้นจึงต้องพึ่งพา Data Model และโครงสร้างของ Channel Account ที่สร้างไว้ใน FND-01 เพื่อทำ Destination Mapping
- **พึ่งพา FND-03 (Webhook Endpoints & Signature Verification):** ใน ACE-689 ระบุไว้ชัดเจนใน Technical Notes ว่า "Public webhook endpoint already exists from FND 03" หมายความว่า ACE-689 ไม่ต้องทำหน้าด่านเพื่อรับ Webhook จากข้างนอกหรือทำการเช็ค Signature ของ LINE เอง แต่จะรอรับ Event Payload ที่ผ่านการ Verify ความถูกต้องจาก FND-03 เรียบร้อยแล้ว นำมาประมวลผลต่อ

### 2. Dependency กับ EPIC-ACE-31 (NDP - Normalization and Data Persistence)
- **พึ่งพา NDP-01 (Define Normalized Schema v1):** ACE-689 ระบุว่าต้องแปลงข้อมูลเป็น Schema v1 (Contact, Conversation, Message) ซึ่งรายละเอียดของ Schema เหล่านี้ถูกออกแบบและนิยามไว้ในโครงสร้างหลักของ NDP-01
- **พึ่งพา NDP-02 (Implement Message Persistence v1):** ACE-689 ต้องการบันทึกข้อมูลและมีการป้องกันข้อมูลซ้ำ (Idempotency) ซึ่งกระบวนการยิบย่อยเช่น การ Upsert Contact, สร้าง Conversation, และการ Insert Message จะใช้ Service / Repository pattern ที่พัฒนาไว้เป็นแพทเทิร์นส่วนกลางใน NDP-02
- **พึ่งพา NDP-03 (Normalization Pipeline v1):** ACE-689 จะเข้าไปใช้ "Internal ingest entrypoint or queue consumer" จาก NDP-03 หมายความว่า ACE-689 (ที่เป็นแพตเทิร์นดึงข้อความของ LINE) จะเป็นแค่หนึ่งใน Flow ที่ไปเสียบใช้งานบน Pipeline/Queue มาตรฐานที่มีโครงข่ายพื้นฐานเตรียมไว้แล้วใน NDP-03 

### สรุปภาพรวมการทำงาน
- **EPIC-ACE-30:** เป็น "ประตูหน้าบ้าน" (ตั้ง Webhook ยืนยันตัวตน, มีข้อมูล Tenant)
- **EPIC-ACE-31:** เป็น "โครงสร้างแกนกลาง" (มี Pipeline จัดการต่อคิว, มี Schema ตาราง DB ชัดเจน)
- **ACE-689 (ของ EPIC-ACE-32):** นำ Event ของ LINE ที่ผ่านประตูหน้าบ้านมาแล้ว ไหลเข้าไปไปยังท่อโครงสร้างแกนกลาง เพื่อให้ข้อมูลของ LINE Text Message โผล่ใน Unified Inbox ได้อย่างสมบูรณ์

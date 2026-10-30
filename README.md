## 📊 System Flowchart
```mermaid
flowchart TD
    Start([เริ่มต้นโปรแกรม: Streamlit โหลดแอป]) --> Init[กำหนดค่าเริ่มต้นระบบ Session State]
    Init --> CheckLogin{ตรวจสอบสถานะเข้าสู่ระบบ}
    CheckLogin -- "ยังไม่เข้าสู่ระบบ" --> LoginScreen[หน้าจอเข้าสู่ระบบ / สมัครสมาชิก]
    CheckLogin -- "เข้าสู่ระบบแล้ว" --> CheckAdmin{ตรวจสอบสิทธิ์ Admin?}
    CheckAdmin -- "Admin" --> AdminDash[Admin Dashboard]
    CheckAdmin -- "Customer" --> Storefront[Customer Storefront]

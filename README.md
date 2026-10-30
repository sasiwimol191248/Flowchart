flowchart TD
    Start([เริ่มต้นโปรแกรม: Streamlit โหลดแอป]) --> Init[กำหนดค่าเริ่มต้นระบบ Session State<br>• logged_in, cart, orders_db, registered_users]
    Init --> CheckLogin{ตรวจสอบสถานะ:<br>เข้าสู่ระบบแล้วหรือยัง?}
    
    %% กรณียังไม่ล็อกอิน
    CheckLogin -- "ยังไม่ได้เข้าสู่ระบบ (False)" --> LoginScreen[แสดงหน้าจอเข้าสู่ระบบ / สมัครสมาชิก]
    LoginScreen --> InputLogin[ผู้ใช้กรอกข้อมูล Username / Password และกดปุ่ม]
    InputLogin --> ValidLogin{ข้อมูลถูกต้องและครบถ้วน?}
    
    ValidLogin -- "ไม่ใช่" --> ErrLogin[แสดงข้อความแจ้งเตือน Error]
    ErrLogin --> LoginScreen
    
    ValidLogin -- "ใช่" --> SetLogin[บันทึก logged_in = True, กำหนด username และสิทธิ์ is_admin]
    SetLogin --> Start

    %% กรณีล็อกอินแล้ว
    CheckLogin -- "เข้าสู่ระบบแล้ว (True)" --> CheckAdmin{ตรวจสอบสิทธิ์:<br>is_admin == True หรือไม่?}
    
    %% ฝั่ง Admin
    CheckAdmin -- "ใช่ (Admin)" --> AdminDash[แสดงหน้าหลังบ้าน Admin Dashboard]
    AdminDash --> ViewOrders[ตรวจสอบรายการออเดอร์ทั้งหมดใน orders_db]
    ViewOrders --> ActionAdmin{แอดมินเลือกการจัดการสถานะ}
    
    ActionAdmin -- "อนุมัติ / ทำเสร็จแล้ว" --> UpdateSuccess[อัปเดตสถานะเป็น ดำเนินการสำเร็จแล้ว]
    ActionAdmin -- "เปลี่ยนสถานะกลับ" --> UpdatePending[อัปเดตสถานะเป็น รอดำเนินการ]
    UpdateSuccess --> ViewOrders
    UpdatePending --> ViewOrders
    
    AdminDash --> LogoutAdmin[คลิกปุ่มออกจากระบบ ➔ รีเซ็ต logged_in = False]
    LogoutAdmin --> Start

    %% ฝั่ง Customer
    CheckAdmin -- "ไม่ใช่ (Customer)" --> Storefront[แสดงหน้าร้านค้า Customer Storefront]
    Storefront --> LogoutCust[คลิกปุ่มออกจากระบบ ➔ รีเซ็ต logged_in = False และล้างตะกร้า]
    LogoutCust --> Start

    Storefront --> TabChoice{เลือกแท็บการใช้งาน}
    
    %% แท็บที่ 1 สั่งซื้อปลีก
    TabChoice --> TabRetail[แถบที่ 1: สั่งซื้อปลีกทั่วไป]
    TabRetail --> ShowProducts[แสดงสินค้าแนะนำ 6 รายการ พร้อมปุ่มเพิ่มลงตะกร้า]
    ShowProducts --> AddCart[ผู้ใช้คลิกปุ่มเพิ่มสินค้าลงตะกร้า]
    AddCart --> CalcCart[คำนวณราคารวม & สุ่มส่วนลดพิเศษ 5-20%]
    CalcCart --> InputDetail[กรอกเบอร์โทรศัพท์ & ที่อยู่จัดส่งในตะกร้า]
    InputDetail --> SelectPay[เลือกช่องทางชำระเงิน:<br>1. โอนผ่านธนาคาร/พร้อมเพย์ หรือ 2. เงินสด]
    SelectPay --> ConfirmRetail{คลิกปุ่มยืนยันการสั่งซื้อปลีก}
    
    ConfirmRetail -- "กรอกข้อมูลไม่ครบ" --> ErrRetail[แสดงข้อความแจ้งเตือนให้กรอกข้อมูลให้ครบถ้วน]
    ErrRetail --> InputDetail
    
    ConfirmRetail -- "กรอกข้อมูลครบถ้วน" --> SaveRetail[บันทึกออเดอร์ลง orders_db<br>• แสดงลูกโป่งฉลอง (Balloons)<br>• แสดงข้อมูลชำระเงิน/QR Code พร้อมเพย์ หรือยอดเงินสด]
    SaveRetail --> Storefront

    %% แท็บที่ 2 Pre-order
    TabChoice --> TabBulk[แถบที่ 2: ระบบจองคิว Pre-order หลายรายการ]
    TabBulk --> BulkForm[เลือกติ๊กถูกสินค้าหลายชนิด + กำหนดจำนวนกระถาง]
    BulkForm --> BulkInfo[กำหนดวันที่ใช้งานจริง, เบอร์โทรศัพท์, ที่อยู่, และโน้ตเพิ่มเติม]
    BulkInfo --> SelectBulkPay[เลือกช่องทางชำระเงินมัดจำ:<br>• โอนมัดจำผ่านพร้อมเพย์ หรือ ชำระเต็มจำนวน]
    SelectBulkPay --> ConfirmBulk{คลิกปุ่มส่งคำขอจองคิว Pre-order}
    
    ConfirmBulk -- "กรอกข้อมูลไม่ครบ" --> ErrBulk[แสดงข้อความแจ้งเตือนให้กรอกข้อมูลให้ครบถ้วน]
    ErrBulk --> BulkInfo
    
    ConfirmBulk -- "กรอกข้อมูลครบถ้วน" --> SaveBulk[บันทึกคำขอลง orders_db<br>• แสดงลูกโป่งฉลอง (Balloons)<br>• แสดง QR Code มัดจำพร้อมเพย์หรือยอดสุทธิ]
    SaveBulk --> Storefront

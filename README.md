# การใช้งาน JBoss Middleware

หรือที่รู้จักในชื่อ **JBoss Application Server** หรือ **WildFly** เป็นการตั้งค่าและใช้แพลตฟอร์มที่รองรับการพัฒนาและประมวลผลแอปพลิเคชัน Java EE (Enterprise Edition) ซึ่งรองรับเทคโนโลยีต่าง ๆ เช่น Servlets, EJB (Enterprise JavaBeans), JSP (JavaServer Pages), และอื่น ๆ

ขั้นตอนการใช้งาน JBoss Middleware เบื้องต้นจะมีดังนี้:

### 1. **ติดตั้ง JBoss/WildFly**

* ไปที่เว็บไซต์ [WildFly](https://wildfly.org/downloads/) (ซึ่งเป็นเวอร์ชันใหม่ของ JBoss) เพื่อดาวน์โหลดไฟล์ที่เหมาะสมกับระบบปฏิบัติการที่ใช้งาน
* ดาวน์โหลดไฟล์ `.zip` หรือ `.tar.gz` และแตกไฟล์ไว้ในตำแหน่งที่ต้องการ

```sh
https://github.com/wildfly/wildfly/releases/download/36.0.1.Final/wildfly-36.0.1.Final.zip

https://github.com/wildfly/wildfly/releases/download/36.0.1.Final/wildfly-36.0.1.Final.tar.gz

tar -zxvf wildfly-36.0.1.Final.tar.gz
```

### 2. **ตั้งค่าระบบหลังจากการติดตั้ง**

* หลังจากการติดตั้งเสร็จสิ้น ให้เปิดคอนโซล (Command Prompt หรือ Terminal) และไปยังโฟลเดอร์ที่ติดตั้ง WildFly หรือ JBoss
* เรียกใช้งานเซิร์ฟเวอร์โดยใช้คำสั่งต่อไปนี้:

  * **สำหรับ Windows:**

    ```bash
    bin\standalone.bat
    ```
  * **สำหรับ Linux/Mac:**

    ```bash
    ./bin/standalone.sh
    ```

การเรียกใช้คำสั่งนี้จะทำให้เซิร์ฟเวอร์ WildFly/JBoss เริ่มทำงานในโหมด `standalone` และฟังอยู่ที่พอร์ต 8080 โดยปกติ

### 3. **เข้าถึงการจัดการผ่าน Admin Console**

* เปิดเบราว์เซอร์และไปที่ `http://localhost:9990` ซึ่งเป็นพอร์ตสำหรับการจัดการเซิร์ฟเวอร์
* หากเป็นครั้งแรกที่เข้าถึง จะต้องตั้งรหัสผ่านสำหรับการเข้าถึง Console โดยไปที่โฟลเดอร์ `bin` และใช้คำสั่ง:

  ```bash
  ./add-user.sh
  ```

  หรือใน Windows:

  ```bash
  add-user.bat
  ```

  เลือก `Management User` และตั้งค่าผู้ใช้งานและรหัสผ่าน

### 4. **นำแอปพลิเคชันของคุณขึ้นเซิร์ฟเวอร์**

* หากคุณพัฒนาแอปพลิเคชัน Java EE หรือ WAR (Web Application Archive), คุณสามารถอัปโหลดแอปพลิเคชันไปยังเซิร์ฟเวอร์ได้โดยการคัดลอกไฟล์ `.war` ไปที่โฟลเดอร์ `standalone/deployments/`
* หลังจากนั้น WildFly/JBoss จะทำการ Deploy แอปพลิเคชันโดยอัตโนมัติ และคุณสามารถเข้าถึงแอปพลิเคชันผ่านเบราว์เซอร์ที่ `http://localhost:8080/yourapp`

### 5. **การดูบันทึกของเซิร์ฟเวอร์**

* บันทึก (logs) จะถูกเก็บไว้ในโฟลเดอร์ `standalone/log/`
* คุณสามารถดูบันทึกล่าสุดของเซิร์ฟเวอร์ได้โดยใช้คำสั่ง:

  ```bash
  tail -f standalone/log/server.log
  ```

### ตัวอย่างการ Deploy แอปพลิเคชันใน WildFly:

#### 1. สร้างแอปพลิเคชัน Servlet

* สร้างไฟล์ `HelloServlet.java` ที่มีเนื้อหาดังนี้:

```java
import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<h1>Hello from JBoss/WildFly</h1>");
    }
}
```

#### 2. สร้างไฟล์ `web.xml`

* ไฟล์ `web.xml` เป็นการตั้งค่าการทำงานของ servlet บนเซิร์ฟเวอร์:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
                             http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         version="3.0">
    <servlet>
        <servlet-name>HelloServlet</servlet-name>
        <servlet-class>HelloServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>HelloServlet</servlet-name>
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

#### 3. สร้าง WAR และ Deploy

* คอมไพล์แอปพลิเคชันให้เป็นไฟล์ `.war`
* คัดลอกไฟล์ `.war` ไปที่ `standalone/deployments/`

#### 4. ทดสอบการทำงาน

* เปิดเบราว์เซอร์และไปที่ `http://localhost:8080/yourapp/hello`
* ถ้าทุกอย่างทำงานถูกต้อง คุณจะเห็นข้อความ "Hello from JBoss/WildFly"

### 6. **การใช้ DataSource (การเชื่อมต่อกับฐานข้อมูล)**

* คุณสามารถตั้งค่า DataSource บน WildFly ได้โดยการแก้ไขไฟล์ `standalone.xml` ที่ตั้งอยู่ในโฟลเดอร์ `configuration` ของการติดตั้ง WildFly:

  ```xml
  <datasource jndi-name="java:/MyDataSource" pool-name="MyDataSource" enabled="true" use-java-context="true">
      <connection-url>jdbc:mysql://localhost:3306/mydatabase</connection-url>
      <driver>mysql</driver>
      <security>
          <user-name>root</user-name>
          <password>password</password>
      </security>
  </datasource>
  ```
* อย่าลืมเพิ่มไดรเวอร์ MySQL ที่โฟลเดอร์ `modules` ของ WildFly และกำหนดเส้นทางให้กับไดรเวอร์ใน `standalone.xml`

### 7. **การใช้ EJB (Enterprise JavaBeans)**

* EJB เป็นเทคโนโลยีที่ช่วยในการพัฒนาแอปพลิเคชันที่มีธุรกิจตรรกะซับซ้อน
* สร้าง Interface และ Bean class สำหรับ EJB
* จากนั้น deploy ไปยัง JBoss/WildFly ผ่านการตั้งค่า `jboss-ejb3.xml`

### 8. **การใช้ JMS (Java Message Service)**

* JBoss/WildFly รองรับ JMS สำหรับการทำงานกับข้อความ (messaging)
* ตั้งค่าใน `standalone.xml` หรือผ่านการจัดการใน Admin Console

### สรุป

การใช้งาน JBoss/WildFly เริ่มต้นจากการติดตั้งเซิร์ฟเวอร์, ตั้งค่าผู้ใช้งาน, และ deploy แอปพลิเคชันไปยังเซิร์ฟเวอร์ผ่านการคัดลอกไฟล์ WAR หรือ EAR ลงในโฟลเดอร์ `deployments` จากนั้นคุณสามารถตรวจสอบผลลัพธ์ผ่าน URL ที่กำหนด และสามารถใช้งานฟีเจอร์ต่าง ๆ เช่น DataSource, EJB, และ JMS ได้ตามต้องการ

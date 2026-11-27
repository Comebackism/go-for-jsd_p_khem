# go-for-jsd_p_khem

Go (Gin) + MongoDB REST API พร้อม Swagger UI — โปรเจกต์ฝึกงาน JSD

## Stack

| ส่วน | เทคโนโลยี |
|---|---|
| ภาษา | Go 1.23.4+ |
| HTTP framework | [Gin](https://github.com/gin-gonic/gin) v1.10.0 |
| Database | MongoDB ([mongo-driver](https://github.com/mongodb/mongo-go-driver) v1.17.1) |
| API docs | [swaggo/gin-swagger](https://github.com/swaggo/gin-swagger) |
| Env | [joho/godotenv](https://github.com/joho/godotenv) |

## โครงสร้างโปรเจกต์

```
main.go          entry point — โหลด .env, ต่อ MongoDB, สร้าง router + Swagger
config/db.go     เชื่อมต่อ MongoDB
routes/          นิยาม route
controllers/     handler (user CRUD)
models/          struct ของ document
middlewares/     timeout middleware
common/          request / response struct
docs/            Swagger spec ที่ generate แล้ว (committed)
lesson0/         ตัวอย่างภาษา Go พื้นฐาน (hello world, goroutine, pointer, error handling)
```

## การรันบนเครื่อง (local)

### 1. ติดตั้ง Go

```powershell
winget install GoLang.Go
```

### 2. ติดตั้ง MongoDB (local — ฟรี ไม่ต้องใช้ Atlas)

```powershell
winget install MongoDB.Server
```

ค่าเริ่มต้นจะติดตั้งเป็น Windows service ชื่อ `MongoDB` ฟังที่ `localhost:27017` (data อยู่ใต้โฟลเดอร์ที่ติดตั้ง)

ถ้าต้องการกำหนดไดร์ฟ/พาธเอง ให้ติดตั้งผ่าน msiexec:

```powershell
msiexec /i mongodb-windows-x86_64-9.0-signed.msi /qb ^
  INSTALLLOCATION="D:\MongoDB\Server\9.0\" ^
  ADDLOCAL="ServerService" ^
  SHOULD_INSTALL_COMPASS="0"
```

### 3. ตั้งค่า .env

```powershell
Copy-Item .env-example .env
```

แล้วแก้ `MONGODB_URI` ให้เป็น:

```env
MONGODB_URI=mongodb://localhost:27017
```

### 4. รัน

```powershell
go mod download
go run .
```

- API: <http://localhost:8421>
- Swagger UI: <http://localhost:8421/swagger/index.html>

## Endpoints

| Method | Path | คำอธิบาย |
|---|---|---|
| `POST` | `/users/` | สร้าง user |
| `GET` | `/users/` | ดู user ทั้งหมด |
| `GET` | `/users/{id}` | ดู user ตาม id |
| `DELETE` | `/users/{id}` | ลบ user |

ตัวอย่าง `POST /users/`:

```json
{
  "Name": "สมชาย ใจดี",
  "Email": "somchai@example.com",
  "Password": "1234567",
  "MyCustomKey": 1234
}
```

> `MyCustomKey` เป็น `int` และ `binding:"required"` → ต้องส่งค่าที่ไม่ใช่ `0` ไม่งั้นได้ `400 Bad Request`

## หมายเหตุ

- อย่ารัน `go build ./...` — จะ error เพราะใน `lesson0/` มี `func main()` ซ้ำกันหลายไฟล์ ให้ build เฉพาะ root package: `go build .` หรือ `go run .`
- รหัสผ่านถูกเก็บเป็น plaintext ในฐานข้อมูล (ตามโค้ดต้นฉบับ) — ควร hash ก่อนใช้งานจริง
- ไม่ต้องติดตั้ง `swag` CLI ถ้าไม่ต้อง regenerate Swagger docs (โฟลเดอร์ `docs/` ถูก commit ไว้แล้ว)

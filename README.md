## BACKEND CONTINUATION: Node.js + MongoDB

Convert the React Attendance Tracker into a full‑stack application with a REST API, persistent storage, and clean separation of concerns.

Principle
API first, schema first, then integration (real‑world workflow)

---

## BACKEND TECH STACK

* Node.js (LTS)
* Express.js
* MongoDB Atlas
* Mongoose ODM
* dotenv
* cors
* nodemon (dev only)

---

## TASK 6: Backend Project Setup

### Folder Structure

```
attendance-backend/
 ├─ src/
 │   ├─ config/
 │   │   └─ db.js
 │   ├─ models/
 │   │   └─ Student.js
 │   ├─ routes/
 │   │   └─ attendance.routes.js
 │   ├─ controllers/
 │   │   └─ attendance.controller.js
 │   └─ server.js
 ├─ .env
 └─ package.json
```

### Initialize Project

```
npm init -y
npm install express mongoose cors dotenv
npm install --save-dev nodemon
```

### package.json (scripts)

```
"scripts": {
  "dev": "nodemon src/server.js"
}
```

## TASK 7: MongoDB Connection (Mongoose)

### .env

```
PORT
MONGO_URI
```

### src/config/db.js

```
---

## TASK 8: Student Attendance Schema

### src/models/Student.js

```
import mongoose from "mongoose";

const studentSchema = new mongoose.Schema({
  roll: { type: String, required: true },
  name: { type: String, required: true },
  status: {
    type: String,
    enum: ["present", "absent"],
    default: "absent"
  },
  remarks: { type: String }
}, { timestamps: true });

export default mongoose.model("Student", studentSchema);
```

Design Notes

* enum enforces valid attendance states
* timestamps are industry standard

---

## TASK 9: Attendance API (CRUD)

### API Endpoints

| Method | Endpoint            | Description       |
| ------ | ------------------- | ----------------- |
| GET    | /api/attendance     | Get all students  |
| POST   | /api/attendance     | Add student       |
| PUT    | /api/attendance/:id | Update attendance |
| DELETE | /api/attendance/:id | Remove student    |

### src/controllers/attendance.controller.js

```
import Student from "../models/Student.js";

export const getStudents = async (req, res) => {
  const students = await Student.find();
  res.json(students);
};

export const addStudent = async (req, res) => {
  const student = await Student.create(req.body);
  res.status(201).json(student);
};

export const updateStudent = async (req, res) => {
  const updated = await Student.findByIdAndUpdate(
    req.params.id,
    req.body,
    { new: true }
  );
  res.json(updated);
};

export const deleteStudent = async (req, res) => {
  await Student.findByIdAndDelete(req.params.id);
  res.json({ message: "Student removed" });
};
```

---

### src/routes/attendance.routes.js

```
import express from "express";
import {
  getStudents,
  addStudent,
  updateStudent,
  deleteStudent
} from "../controllers/attendance.controller.js";

const router = express.Router();

router.get("/", getStudents);
router.post("/", addStudent);
router.put("/:id", updateStudent);
router.delete("/:id", deleteStudent);

export default router;
```

---

### src/server.js

```
import express from "express";
import cors from "cors";
import dotenv from "dotenv";
import connectDB from "./config/db.js";
import attendanceRoutes from "./routes/attendance.routes.js";

dotenv.config();
connectDB();

const app = express();
app.use(cors());
app.use(express.json());

app.use("/api/attendance", attendanceRoutes);

app.listen(process.env.PORT, () =>
  console.log(`Server running on port ${process.env.PORT}`)
);
```

Expected Output

* API responds via Postman / Thunder Client

---

## TASK 10: Frontend → Backend Integration

### Replace Static Data

* Remove hardcoded student array
* Fetch data from backend using useEffect

```
useEffect(() => {
  fetch("http://localhost:5000/api/attendance")
    .then(res => res.json())
    .then(data => setStudents(data));
}, []);
```

### Update Attendance

* Use PUT request when toggling status
* Sync UI with DB response

---

## TASK 11: Environment Separation

Frontend (.env)

```
VITE_API_URL=http://localhost:5000/api
```

Backend (.env)

```
PORT=5000
MONGO_URI=...
```

Why This Matters

* Required for deployment
* Prevents hard‑coded URLs

---

## FINAL OUTCOME (Industry Alignment)

Students will understand:

* REST API design
* MongoDB schema modeling
* Frontend ↔ Backend data flow
* State synchronization
* Real project folder structure

This is equivalent to a junior‑level full‑stack project.

---

## NEXT OPTIONAL EXTENSIONS

* Authentication (JWT)
* Role‑based access (Admin / Teacher)
* Class‑wise attendance
* Date‑wise attendance records
* Deployment (Render + MongoDB Atlas)

---

END OF WORKSHEET

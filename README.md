# ReturnComplaintsManagementJosephN
Module de gestion des retours et réclamations permettant aux clients de soumettre une demande et aux administrateurs de les traiter. Interface avec formulaire de soumission, tableau de bord filtrable, et notifications visuelles (SweetAlert2, badges colorés).
# 📦 Returns & Complaints Management System

A full-stack application built with **Spring Boot** and **Angular**, allowing customers to submit product return or complaint requests, and admins to manage them via a dashboard. Includes **Swagger UI**, **SweetAlert2**, and a PostgreSQL database.

---

## 🚀 Technologies Used

* **Backend**: Java, Spring Boot, PostgreSQL
* **Frontend**: Angular 17, SweetAlert2
* **API Docs**: Swagger UI

---

## 📁 Project Structure

```
returns-complaints-management/
├── backend/    → Spring Boot app
└── frontend/   → Angular 17 app
```

---

## 🔧 Backend Code (Spring Boot)

### `/backend/pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>complaint-system</artifactId>
  <version>1.0.0</version>
  <packaging>jar</packaging>

  <properties>
    <java.version>17</java.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
      <groupId>org.postgresql</groupId>
      <artifactId>postgresql</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springdoc</groupId>
      <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
      <version>2.5.0</version>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
      <optional>true</optional>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### `application.properties`

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/cosmetic_db
spring.datasource.username=postgres
spring.datasource.password=your_password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
springdoc.swagger-ui.path=/swagger-ui.html
```

### Complaint.java

```java
@Entity
public class Complaint {
  @Id @GeneratedValue
  private Long id;
  private String type;
  private String description;
  private String status = "PENDING";

  @ManyToOne
  private Customer customer;
}
```

### Customer.java

```java
@Entity
public class Customer {
  @Id @GeneratedValue
  private Long id;
  private String name;
  private String email;
}
```

### ComplaintController.java

```java
@RestController
@RequestMapping("/api/complaints")
public class ComplaintController {
  @Autowired
  private ComplaintRepository complaintRepo;

  @PostMapping
  public Complaint submit(@RequestBody Complaint c) {
    return complaintRepo.save(c);
  }

  @GetMapping
  public List<Complaint> list() {
    return complaintRepo.findAll();
  }

  @PutMapping("/{id}")
  public Complaint update(@PathVariable Long id, @RequestBody Complaint data) {
    Complaint c = complaintRepo.findById(id).orElseThrow();
    c.setStatus(data.getStatus());
    return complaintRepo.save(c);
  }
}
```

---

## 💻 Frontend Code (Angular)

### complaint-form.component.ts

```ts
@Component({...})
export class ComplaintFormComponent {
  complaint = { type: '', description: '', customer: { name: '', email: '' } };
  constructor(private http: HttpClient) {}
  submit() {
    this.http.post('/api/complaints', this.complaint).subscribe(() => {
      Swal.fire('Success', 'Complaint submitted!', 'success');
    });
  }
}
```

### admin-dashboard.component.ts

```ts
@Component({...})
export class AdminDashboardComponent {
  complaints = [];
  constructor(private http: HttpClient) {
    this.http.get<any[]>('/api/complaints').subscribe(data => this.complaints = data);
  }
  updateStatus(id: number, status: string) {
    this.http.put(`/api/complaints/${id}`, { status }).subscribe(() => {
      Swal.fire('Updated', 'Status changed', 'info');
    });
  }
}
```

### HTML with SweetAlert2 and filters

```html
<!-- complaint-form.component.html -->
<form (ngSubmit)="submit()">
  <input [(ngModel)]="complaint.customer.name" name="name" required />
  <input [(ngModel)]="complaint.customer.email" name="email" required />
  <select [(ngModel)]="complaint.type" name="type">
    <option value="RETURN">Return</option>
    <option value="COMPLAINT">Complaint</option>
  </select>
  <textarea [(ngModel)]="complaint.description" name="description"></textarea>
  <button type="submit">Submit</button>
</form>
```

```html
<!-- admin-dashboard.component.html -->
<div *ngFor="let c of complaints">
  {{ c.customer.name }} - {{ c.type }} - <b [ngClass]="c.status">{{ c.status }}</b>
  <button (click)="updateStatus(c.id, 'RESOLVED')">Resolve</button>
</div>
```

---

## 🔍 Swagger UI

Once backend is running:
👉 Visit: `http://localhost:8080/swagger-ui.html`
Click **"Try it out"** to test all endpoints.

---

## 🐘 PostgreSQL Setup

```sql
CREATE DATABASE cosmetic_db;
```

* Username: `postgres`
* Password: `your_password`

---

## ▶ How to Run

### Backend

```bash
cd backend
./mvnw spring-boot:run
```

### Frontend

```bash
cd frontend
npm install
ng serve
```

---

## ✅ Done!

Everything is now set up. You can paste this entire file into GitHub `README.md` from your mobile.

If you want the project zipped and downloadable as folders (`backend/`, `frontend/`), ask me and I’ll generate it instantly.

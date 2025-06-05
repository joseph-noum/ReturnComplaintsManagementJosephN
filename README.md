# ReturnComplaintsManagement : Auteur: Ingénieur NOUMOINVI Joseph

Module de gestion des retours et réclamations permettant aux clients de soumettre une demande et aux administrateurs de les traiter. 
Interface avec formulaire de soumission, tableau de bord filtrable, et notifications visuelles (SweetAlert2, badges colorés).

# 📦 Returns & Complaints Management System

Application Fullstack réalisée à l'aide des technologies **Spring Boot** et **Angular**, permettant aux clients de soumettre des réclamations et aux administrateurs de traiter les demandes depuis un interface 

Includes **Swagger UI**, **SweetAlert2**, and a PostgreSQL database.

---

## 🚀 Technologies utilisées

* **Backend** : Java, Spring Boot, PostgreSQL
* **Frontend** : Angular 17, SweetAlert2
* **API Docs** : Swagger UI

---

## 📁 Structure du projet

```
returns-complaints-management/
├── backend/    → Application Spring Boot
└── frontend/   → Application Angular 17
```

---

## 🔧 Backend Spring Boot (avec commentaires)

### `/backend/pom.xml`

```xml
<!-- Fichier de configuration Maven -->
<project ...>
  <properties>
    <java.version>17</java.version>
  </properties>
  <dependencies>
    <!-- Dépendance web Spring -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <!-- Accès aux données avec JPA -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <!-- Connecteur PostgreSQL -->
    <dependency>
      <groupId>org.postgresql</groupId>
      <artifactId>postgresql</artifactId>
    </dependency>
    <!-- Swagger UI pour documentation API -->
    <dependency>
      <groupId>org.springdoc</groupId>
      <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
      <version>2.5.0</version>
    </dependency>
  </dependencies>
</project>
```

### `application.properties`

```properties
# Configuration de la base PostgreSQL
spring.datasource.url=jdbc:postgresql://localhost:5432/cosmetic_db
spring.datasource.username=postgres
spring.datasource.password=your_password
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true

# Swagger UI
springdoc.swagger-ui.path=/swagger-ui.html
```

### `Complaint.java`

```java
// Entité représentant une réclamation client
@Entity
public class Complaint {
  @Id @GeneratedValue
  private Long id;
  private String type; // Type : retour ou plainte
  private String description; // Description de la demande
  private String status = "PENDING"; // Statut par défaut

  @ManyToOne
  private Customer customer; // Réclamation liée à un client
}
```

### `Customer.java`

```java
// Entité représentant un client
@Entity
public class Customer {
  @Id @GeneratedValue
  private Long id;
  private String name; // Nom du client
  private String email; // Email du client
}
```

### `ComplaintController.java`

```java
// Contrôleur REST pour gérer les requêtes liées aux réclamations
@RestController
@RequestMapping("/api/complaints")
public class ComplaintController {

  @Autowired
  private ComplaintRepository complaintRepo;

  // Créer une nouvelle réclamation
  @PostMapping
  public Complaint submit(@RequestBody Complaint c) {
    return complaintRepo.save(c);
  }

  // Lister toutes les réclamations
  @GetMapping
  public List<Complaint> list() {
    return complaintRepo.findAll();
  }

  // Modifier le statut d'une réclamation
  @PutMapping("/{id}")
  public Complaint update(@PathVariable Long id, @RequestBody Complaint data) {
    Complaint c = complaintRepo.findById(id).orElseThrow();
    c.setStatus(data.getStatus());
    return complaintRepo.save(c);
  }
}
```

---

## 💻 Frontend Angular (avec commentaires)

### `complaint-form.component.ts`

```ts
// Composant Angular pour envoyer une réclamation
@Component({...})
export class ComplaintFormComponent {
  // Objet de réclamation
  complaint = { type: '', description: '', customer: { name: '', email: '' } };

  constructor(private http: HttpClient) {}

  // Soumettre le formulaire de réclamation
  submit() {
    this.http.post('/api/complaints', this.complaint).subscribe(() => {
      Swal.fire('Succès', 'Réclamation envoyée !', 'success');
    });
  }
}
```

### `admin-dashboard.component.ts`

```ts
// Composant Angular pour l'admin : visualisation + mise à jour des réclamations
@Component({...})
export class AdminDashboardComponent {
  complaints = [];

  constructor(private http: HttpClient) {
    this.http.get<any[]>('/api/complaints').subscribe(data => this.complaints = data);
  }

  updateStatus(id: number, status: string) {
    this.http.put(`/api/complaints/${id}`, { status }).subscribe(() => {
      Swal.fire('Mis à jour', 'Statut modifié', 'info');
    });
  }
}
```

### `HTML`

```html
<!-- Formulaire de soumission -->
<form (ngSubmit)="submit()">
  <input [(ngModel)]="complaint.customer.name" name="name" required />
  <input [(ngModel)]="complaint.customer.email" name="email" required />
  <select [(ngModel)]="complaint.type" name="type">
    <option value="RETURN">Retour</option>
    <option value="COMPLAINT">Réclamation</option>
  </select>
  <textarea [(ngModel)]="complaint.description" name="description"></textarea>
  <button type="submit">Envoyer</button>
</form>
```

```html
<!-- Tableau de bord admin -->
<div *ngFor="let c of complaints">
  {{ c.customer.name }} - {{ c.type }} - <b [ngClass]="c.status">{{ c.status }}</b>
  <button (click)="updateStatus(c.id, 'RESOLVED')">Marquer comme résolu</button>
</div>
```

---

## 🔍 Swagger UI (documentation interactive)

Dès que le backend tourne :
👉 Aller sur `http://localhost:8080/swagger-ui.html`
Cliquer sur **Try it out** pour tester les requêtes

---

## 🐘 Configuration de PostgreSQL

Créer la base de données requise :

```sql
CREATE DATABASE cosmetic_db;
```

* Utilisateur : `postgres`
* Mot de passe : `your_password`

---

## ▶ Pour Lancer le projet

### Backend Spring Boot

```bash
cd backend
./mvnw spring-boot:run
```

### Frontend Angular

```bash
cd frontend
npm install
ng serve
```

---

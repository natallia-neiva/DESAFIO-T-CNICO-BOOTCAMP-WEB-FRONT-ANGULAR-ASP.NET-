# DESAFIO-T-CNICO-BOOTCAMP-WEB-FRONT-ANGULAR-ASP.NET-
Desenvolver uma aplicação web simples para cadastro e gerenciamento de tarefas, utilizando Angular no front-end, ASP.NET Core Web API no back-end e SQL Server como banco de dados.

# 📌 Desafio Técnico – Gerenciador de Tarefas

## 🧩 Descrição do Projeto

Aplicação web simples para cadastro e gerenciamento de tarefas, desenvolvida com:

* Front-end: Angular
* Back-end: ASP.NET Core Web API (C#)
* Banco de dados: SQL Server
* ORM: Entity Framework Core
* Comunicação: API REST (JSON)

---

## 📁 Estrutura do Projeto

```
/backend -> API ASP.NET Core
/frontend -> Aplicação Angular
```

---

## 📌 Funcionalidades

* Criar tarefa
* Listar tarefas
* Buscar tarefa por ID
* Atualizar tarefa
* Excluir tarefa

---

## 🧱 Entidade Tarefa

```
Id: int
Titulo: string
Descricao: string
Status: string (Pendente / Concluída)
DataCriacao: DateTime
```

---

## 🔧 Back-end (ASP.NET Core)

### 📂 Estrutura

```
Controllers/
Models/
Data/
```

---

### 📌 Model

```csharp
public class Tarefa
{
    public int Id { get; set; }
    public string Titulo { get; set; }
    public string Descricao { get; set; }
    public string Status { get; set; }
    public DateTime DataCriacao { get; set; } = DateTime.Now;
}
```

---

### 📌 DbContext

```csharp
using Microsoft.EntityFrameworkCore;

public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }

    public DbSet<Tarefa> Tarefas { get; set; }
}
```

---

### 📌 Controller

```csharp
[ApiController]
[Route("api/[controller]")]
public class TarefasController : ControllerBase
{
    private readonly AppDbContext _context;

    public TarefasController(AppDbContext context)
    {
        _context = context;
    }

    [HttpGet]
    public async Task<ActionResult<IEnumerable<Tarefa>>> Get()
    {
        return await _context.Tarefas.ToListAsync();
    }

    [HttpGet("{id}")]
    public async Task<ActionResult<Tarefa>> Get(int id)
    {
        var tarefa = await _context.Tarefas.FindAsync(id);

        if (tarefa == null)
            return NotFound();

        return tarefa;
    }

    [HttpPost]
    public async Task<ActionResult<Tarefa>> Post(Tarefa tarefa)
    {
        _context.Tarefas.Add(tarefa);
        await _context.SaveChangesAsync();

        return CreatedAtAction(nameof(Get), new { id = tarefa.Id }, tarefa);
    }

    [HttpPut("{id}")]
    public async Task<IActionResult> Put(int id, Tarefa tarefa)
    {
        if (id != tarefa.Id)
            return BadRequest();

        _context.Entry(tarefa).State = EntityState.Modified;
        await _context.SaveChangesAsync();

        return NoContent();
    }

    [HttpDelete("{id}")]
    public async Task<IActionResult> Delete(int id)
    {
        var tarefa = await _context.Tarefas.FindAsync(id);

        if (tarefa == null)
            return NotFound();

        _context.Tarefas.Remove(tarefa);
        await _context.SaveChangesAsync();

        return NoContent();
    }
}
```

---

### 📌 Configuração do Banco

```json
"ConnectionStrings": {
  "DefaultConnection": "Server=localhost;Database=TaskDb;Trusted_Connection=True;"
}
```

---

## 🎨 Front-end (Angular)

### 📂 Estrutura

```
components/
services/
models/
```

---

### 📌 Model

```ts
export interface Tarefa {
  id?: number;
  titulo: string;
  descricao: string;
  status: string;
  dataCriacao?: Date;
}
```

---

### 📌 Service

```ts
import { HttpClient } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { Tarefa } from '../models/tarefa';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class TarefaService {

  private api = 'https://localhost:5001/api/tarefas';

  constructor(private http: HttpClient) {}

  getAll(): Observable<Tarefa[]> {
    return this.http.get<Tarefa[]>(this.api);
  }

  getById(id: number): Observable<Tarefa> {
    return this.http.get<Tarefa>(`${this.api}/${id}`);
  }

  create(tarefa: Tarefa) {
    return this.http.post(this.api, tarefa);
  }

  update(id: number, tarefa: Tarefa) {
    return this.http.put(`${this.api}/${id}`, tarefa);
  }

  delete(id: number) {
    return this.http.delete(`${this.api}/${id}`);
  }
}
```

---

### 📌 Exemplo HTML

```html
<div *ngFor="let tarefa of tarefas">
  <h3>{{ tarefa.titulo }}</h3>
  <p>{{ tarefa.descricao }}</p>
  <span>{{ tarefa.status }}</span>

  <button (click)="editar(tarefa)">Editar</button>
  <button (click)="excluir(tarefa.id)">Excluir</button>
</div>
```

---

### 🔹 Backend

```bash
dotnet restore
dotnet ef database update
dotnet run
```

---

### 🔹 Frontend

```bash
npm install
ng serve
```

A aplicação estará disponível em:

```
http://localhost:4200
```

---

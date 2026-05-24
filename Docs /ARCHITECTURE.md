# 🏗️ Arquitetura do Projeto - Event Confirmation System

## 📁 Estrutura do Projeto

```
ProjetoModulo3/
├── app/
│   ├── templates/
│   │   ├── auth/
│   │   │   ├── login.html
│   │   │   └── register.html
│   │   ├── events/
│   │   │   ├── dashboard.html
│   │   │   ├── create.html
│   │   │   ├── edit.html
│   │   │   └── share.html
│   │   ├── confirmations/
│   │   │   ├── confirm_page.html
│   │   │   ├── event_page.html
│   │   │   ├── thankyou_confirmed.html
│   │   │   └── thankyou_declined.html
│   │   ├── admin/
│   │   │   └── report.html
│   │   └── base.html
│   ├── routes/
│   │   ├── __init__.py
│   │   ├── auth.py
│   │   ├── events.py
│   │   ├── confirmations.py
│   │   └── admin.py
│   ├── utils/
│   │   ├── __init__.py
│   │   └── email.py
│   ├── static/
│   │   └── uploads/
│   ├── models.py
│   └── __init__.py
├── app.py
├── config.py
├── requirements.txt
├── .env.example
├── README.md
├── BACKEND_SETUP.md
├── MOBILE_OPTIMIZATION.md
└── Docs /
    ├── base.html
    ├── MOBILE_OPTIMIZATION.md
    ├── ARCHITECTURE.md
    ├── API_ENDPOINTS.md
    └── INSTALLATION.md
```

## 🏛️ Camadas da Aplicação

### 1. **Camada de Apresentação (Frontend)**
- Templates HTML responsivos
- CSS moderno com mobile-first approach
- JavaScript vanilla para interatividade
- Suporte a dark mode

### 2. **Camada de Lógica (Routes)**
- **auth.py** - Autenticação e autorização
- **events.py** - CRUD de eventos
- **confirmations.py** - Fluxo de confirmação
- **admin.py** - Relatórios e analytics

### 3. **Camada de Dados (Models)**
- **User** - Usuários/criadores de eventos
- **Event** - Eventos criados
- **Confirmation** - Confirmações de presença

### 4. **Camada de Utilitários**
- **email.py** - Envio de emails async
- **config.py** - Configurações de ambiente

## 🔄 Fluxos Principais

### Fluxo 1: Criação de Evento
```
User (Login) 
  ↓
Dashboard 
  ↓
Create Event 
  ↓
Save to Database 
  ↓
Generate Link 
  ↓
Share Event
```

### Fluxo 2: Confirmação de Presença
```
Guest (Email/Link) 
  ↓
Verify Email 
  ↓
View Event Page 
  ↓
Confirm/Decline 
  ↓
Send Email Receipt 
  ↓
Thank You Page
```

### Fluxo 3: Relatório
```
Event Creator 
  ↓
View Report 
  ↓
See Statistics 
  ↓
Download CSV
```

## 🗄️ Modelo de Dados

### User
```python
id: UUID
email: String (unique)
username: String (unique)
password_hash: String
created_at: DateTime
updated_at: DateTime
events: Relationship
```

### Event
```python
id: UUID
title: String
description: Text
custom_message: Text
background_image: String
confirmation_link: String (unique)
event_date: DateTime
location: String
creator_id: UUID (FK)
created_at: DateTime
updated_at: DateTime
confirmations: Relationship
```

### Confirmation
```python
id: UUID
event_id: UUID (FK)
attendee_name: String
attendee_email: String
status: Enum (pending, confirmed, declined)
email_sent: Boolean
created_at: DateTime
updated_at: DateTime
```

## 🔌 Endpoints da API

### Autenticação
- `GET/POST /auth/register` - Registrar
- `GET/POST /auth/login` - Login
- `GET /auth/logout` - Logout

### Eventos
- `GET /events/dashboard` - Dashboard
- `GET/POST /events/create` - Criar
- `GET/POST /events/<id>/edit` - Editar
- `POST /events/<id>/delete` - Deletar
- `GET /events/<id>/share` - Compartilhar
- `GET /events/<id>/stats` - Estatísticas (JSON)

### Confirmações
- `GET /confirm/<link>` - Página confirmação
- `POST /confirm/<link>/verify-email` - Verificar email
- `GET /confirm/<link>/<id>/event-page` - Página evento
- `POST /confirm/<id>/confirm` - Confirmar
- `POST /confirm/<id>/decline` - Recusar
- `GET /confirm/thankyou/confirmed` - Obrigado
- `GET /confirm/thankyou/declined` - Obrigado

### Admin
- `GET /admin/<id>/report` - Relatório
- `GET /admin/<id>/report/download` - Download CSV

## 🔐 Segurança

### Implementações
- ✅ Hash de senhas (Werkzeug)
- ✅ CSRF Protection
- ✅ SQL Injection prevention (SQLAlchemy ORM)
- ✅ Session security (Flask-Login)
- ✅ Email validation
- ✅ UUID para IDs (segurança)

### Boas Práticas
- ✅ `.env` para variáveis sensíveis
- ✅ HTTPS em produção
- ✅ Validação em backend
- ✅ Rate limiting recomendado
- ✅ Logs de auditoria

## 📊 Diagrama de Fluxo

```
┌─────────────────────────────────────────────────────────┐
│         SISTEMA DE CONFIRMAÇÃO DE EVENTOS              │
└─────────────────────────────────────────────────────────┘

┌────────────────────┐
│  CRIADOR DE EVENTO │
│                    │
│ 1. Login           │
│ 2. Criar evento    │
│ 3. Personalizar    │
│ 4. Gerar link      │
│ 5. Compartilhar    │
│ 6. Ver relatório   │
└────────┬───────────┘
         │
         ├─────────────────────────────┐
         │                             │
         v                             v
    ┌─────────┐                  ┌─────────┐
    │ Database│                  │  Email  │
    │ (PostgreSQL)│             │  Service│
    └────┬────┘                  └─────────┘
         │                             ^
         │                             │
         ├─────────────────────────────┤
         │                             │
         v                             │
┌────────────────────┐                │
│ PARTICIPANTE/GUEST │                │
│                    │                │
│ 1. Recebe link     ├────────────────┘
│ 2. Clica no link   │
│ 3. Verifica email  │
│ 4. Vê evento       │
│ 5. Confirma/Recusa │
│ 6. Obrigado!       │
└────────────────────┘
```

## 🚀 Stack Tecnológico

### Backend
- **Python 3.8+**
- **Flask 2.3.3**
- **PostgreSQL**
- **SQLAlchemy ORM**
- **Flask-Login**
- **Flask-Mail**

### Frontend
- **HTML5**
- **CSS3** (com variáveis, grid, flexbox)
- **JavaScript vanilla**
- **Responsive design**

### Devops
- **pip** (package manager)
- **gunicorn** (production server)
- **dotenv** (config management)

## 📈 Performance

### Otimizações
- ✅ Lazy loading de imagens
- ✅ Async email sending
- ✅ Database indexing
- ✅ Caching de static files
- ✅ Minificação de CSS/JS

### Métricas
- Load time: < 2s
- Page size: < 100KB
- API response: < 200ms

## 🧪 Testes Recomendados

### Unit Tests
- Modelos de dados
- Validações
- Hash de senhas

### Integration Tests
- Fluxos de autenticação
- Fluxos de confirmação
- Envio de emails

### E2E Tests
- Criar evento
- Confirmar presença
- Gerar relatório

## 📚 Documentação Relacionada

- [MOBILE_OPTIMIZATION.md](./MOBILE_OPTIMIZATION.md) - Otimizações mobile
- [API_ENDPOINTS.md](./API_ENDPOINTS.md) - Documentação de endpoints
- [INSTALLATION.md](./INSTALLATION.md) - Guia de instalação

# ButterDigitalClinic (Blazor Server, .NET 9)

ButterDigitalClinic — a .NET 9 Blazor Server app for managing clinic repairs, parts, training classes, and user accounts with Identity, EF Core, and role‑based access.

## Overview
**ButterDigitalClinic** is a Blazor Server (.NET 9) application that streamlines a community clinic’s day‑to‑day operations. 
It covers owner/CTEI registration, repair intake and tracking, parts inventory with low‑stock alerts, volunteer time logging, 
and training class management — all secured with ASP.NET Core Identity (roles for Admin and User) and backed by Entity Framework Core.

The system models the real workflow end‑to‑end: add Owners and their CTEIs (devices/items), create Repairs with status, cost, 
time tracking and part usage, and automatically adjust inventory when parts are consumed. Admins can manage user accounts, 
enable/disable users, and view operational stats (e.g., part usage per month, repair counts/avg cost, volunteer hours) through 
a lightweight Statistics service. Pages follow a consistent, reusable pattern (PageBase and EditablePageBase) to keep code clean, 
testable, and easy to extend.

Under the hood, EF Core handles data access with explicit relationships (e.g., RepairParts join table and RepairUsers for 
hours worked). Identity is pre‑seeded with roles and a default administrator for first‑run access. The UI uses Bootstrap and 
standard Blazor components for a clear, responsive experience.

## Key Features
- **Account & Roles**: ASP.NET Core Identity with seeded **Admin** and **User** roles; enable/disable accounts; optional title field.
- **Owners & CTEIs**: Register owners, link CTEIs, view per‑owner and per‑CTEI history.
- **Repairs**: Create, edit, and view repairs with **status**, **type (Software/Hardware/Both)**, **time taken**, **date completed**, and **total cost**.
- **Parts Inventory**: CRUD parts with **Inventory**, **Minimum**, and automatic **low‑stock flag**; inventory auto‑decrements on part usage.
- **Join Models**: `RepairParts` (with quantity & cost) and `RepairUsers` (with hours worked).
- **Training Classes**: Create classes, assign **DeliveredBy** user, manage attendees, and view details.
- **Statistics**: Monthly part usage, repair counts/average cost, and volunteer hours by user.
- **Reusable Page Pattern**: `PageBase` for lists & navigation; `EditablePageBase<T>` for forms.
- **Unit Tests**: Service tests (xUnit + FluentAssertions) for Owners, CTEIs, Parts, Repairs, Training.

## Project Structure (high level)
```
/Components
  /Layout (MainLayout, NavMenu)
  /Pages
    /Admin (AccountList, AccountEdit, Statistics)
    /Owners (OwnerList, OwnerForm)
    /CTEI (CTEIList, CTEIForm, CTEIRepairHistory)
    /Repairs (RepairList, RepairForm, RepairDetails)
    /Parts (PartsList, PartsForm, PartUsed)
    /Training (TrainingClassList, TrainingClassForm, TrainingClassDetails, TrainingClassAttendees)
/Data (ApplicationDbContext, Migrations, DbInitializer, Roles, ApplicationUser)
/Enums (RepairStatus, TypeOfRepair, Mode, UserTitle)
/Models (Owner, CTEI, Repair, Part, RepairParts, RepairUser, TrainingClass)
/Services (OwnerService, CTEIService, RepairService, PartService, TrainingClassService, StatisticsService)
/wwwroot (Bootstrap, site assets)
Program.cs, appsettings*.json, ButterDigitalClinic_Ver2.csproj
```

## Tech Stack
- **Frontend**: Blazor Server (Razor Components), Bootstrap
- **Backend**: .NET 9, ASP.NET Core, EF Core, ASP.NET Core Identity
- **Database**: SQL Server (LocalDB by default). SQLite or SQL Server in Docker can be used on Linux.
- **Testing**: xUnit, FluentAssertions (see sibling test project folder).

## Getting Started

### 1) Prerequisites
- .NET SDK **9.0**
- SQL Server (LocalDB on Windows), or **SQL Server Docker**/**SQLite** on Linux/macOS

### 2) Configure the database
By default, `appsettings.json` points to SQL Server LocalDB:
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=YOUR_DB;Trusted_Connection=True;MultipleActiveResultSets=true"
  }
}
```
**Linux/macOS options:**
- **Docker SQL Server** (example): update `DefaultConnection` to your container’s connection string.
- **SQLite** (quick dev): switch provider in `ApplicationDbContextFactory.cs` and in `Program.cs` to `UseSqlite(...)`, and set a file path, e.g. `Data Source=clinic.db`.

Run EF Core migrations:
```bash
dotnet tool restore
dotnet restore
dotnet ef database update
```

### 3) Run the app
```bash
dotnet run
```
The app will seed roles and a default admin user on first run.

**Seeded credentials (development only):**
- Email: `admin@clinic.local`
- Password: `P@ssword123`

> Change these in production and consider removing seeding.

## Common Tasks
- **Create a Repair** → attach CTEI, add RepairParts (with quantities), set Type and Status; inventory auto‑updates.
- **Mark Completion** → set `DateCompleted` and `TimeTaken`; appears in Statistics.
- **Manage Training** → create class, set DeliveredBy, add/remove attendees.
- **Account Admin** → enable/disable users, edit details (including Title).

## Tests
A separate test project with service tests exists in the repository (xUnit + FluentAssertions). Run from solution root:
```bash
dotnet test
```


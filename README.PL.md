# ProcureFlow

ProcureFlow to system B2B do obsługi zapotrzebowań zakupowych w organizacji z
wieloma oddziałami. Pracownik tworzy wniosek, manager zatwierdza go w zakresie
budżetu oddziału, a dział Procurement obsługuje eskalację, zamówienie i
dostarczenie.

## Status wydania

Zakres `v1.0.0` jest zaimplementowany jako lokalny release candidate. Formalne
wydanie wymaga świeżego dowodu z CI, stagingu, backupu, odtworzenia i rollbacku.
Liczby testów zapisane w poprzednich audytach nie są dowodem aktualności
bieżącego commita.

Stan produktu i dalsze kroki: [roadmap](doc/ROADMAP.md) oraz
[lista kontrolna release](doc/RELEASE_CHECKLIST.md).

## Zakres produktu

- Jedna organizacja, wiele oddziałów i członkostwa przypisane do oddziału.
- Katalog kategorii, jednostek miary i produktów.
- Draft zapotrzebowania, pozycje, wysłanie i anulowanie.
- Miesięczne budżety oddziałów, akceptacja managera i eskalacja do Procurement.
- Stany zamówienia i dostarczenia.
- Historia decyzji, powiadomienia, załączniki i dashboard.
- Testy transakcji, autoryzacji i współbieżności na PostgreSQL.
- Testy przeglądarkowe krytycznego przepływu zakupowego.

## Stack

- Backend: .NET 10 LTS, ASP.NET Core, EF Core, PostgreSQL, FluentValidation,
  Serilog, xUnit i Moq.
- Frontend: React 19, TypeScript, React Router, React Hook Form, Zod, Vite,
  Vitest, Testing Library i Playwright.

## Szybki start

Wymagania: .NET 10 SDK, Node.js 22 oraz Docker z Compose.

```powershell
git clone https://github.com/Karol8284/ProcureFlow.git
Set-Location ProcureFlow
Copy-Item .env.example .env
docker compose up --build
```

Lokalne adresy:

- frontend: `http://localhost:3000`;
- API: `http://localhost:5000`;
- health: `http://localhost:5000/health`;
- Swagger: `http://localhost:5000/swagger`;
- Mailpit: `http://localhost:8025`.

Domyślny Compose służy do developmentu i smoke testów. Nie jest konfiguracją
produkcyjną.

Pełna instrukcja konfiguracji i uruchamiania:
[Getting Started](doc/GETTING_STARTED.md).

## Walidacja

```powershell
dotnet build backend/backend.slnx --configuration Release
dotnet test backend/UnitTests/UnitTests.csproj --configuration Release
dotnet test backend/IntegrationTests/IntegrationTests.csproj --configuration Release
Set-Location frontend
npm ci
npm run test:once
npm run build
Set-Location ..
.\scripts\Invoke-E2ETests.ps1
```

Testy PostgreSQL wymagają działającego Dockera. Smoke testy i Playwright wymagają
uruchomionej aplikacji albo pełnego skryptu walidacyjnego.

## Dokumentacja

- [Mapa dokumentacji](doc/README.md)
- [Roadmap produktu](doc/ROADMAP.md)
- [Architektura](doc/ARCHITECTURE.md)
- [Backend setup](doc/BACKEND_SETUP.md)
- [Frontend setup](doc/FRONTEND_SETUP.md)
- [CI/CD](doc/CI_CD.md)
- [Runbook wdrożeniowy](doc/DEPLOYMENT_RUNBOOK.md)

## Licencja

MIT. Szczegóły: [LICENSE](LICENSE).

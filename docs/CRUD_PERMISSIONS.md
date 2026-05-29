# CRUD permissions by role

All table data supports **View**, **Create**, **Update**, and **Delete** based on role and user scope. The backend enforces these via `checkRole()` and farm/user scoping; the frontend uses `src/utils/permissions.ts` to show/hide buttons.

## Role scope

- **SUPERADMIN**: All farms, all users; activate farms.
- **ADMIN**: Own farm(s) (owner); users and data for those farms.
- **MANAGER**: Farm(s) they manage; users and data for those farms.
- **VETERINARIAN**: Farm(s) they are assigned to (via Veterinarian record); view cattle, full CRUD on vaccinations/inseminations/health; view own activity logs.

## Backend (route roles)

| Resource | Create | Read | Update | Delete |
|----------|--------|------|--------|--------|
| Users | SUPERADMIN, ADMIN | SUPERADMIN, ADMIN, MANAGER* | (self/team) | SUPERADMIN, ADMIN |
| Farms | SUPERADMIN, ADMIN | all (scoped) | ADMIN, MANAGER | ADMIN, MANAGER |
| Cattle | SUPERADMIN, ADMIN, MANAGER | + VETERINARIAN | SUPERADMIN, ADMIN, MANAGER | SUPERADMIN, ADMIN, MANAGER |
| Production | SUPERADMIN, ADMIN, MANAGER | same | same | same |
| Production totals | ADMIN, MANAGER | + SUPERADMIN | ADMIN, MANAGER | ADMIN, MANAGER |
| Production transactions | ADMIN, MANAGER | SUPERADMIN, ADMIN, MANAGER | ADMIN, MANAGER | ADMIN, MANAGER |
| Waste logs | SUPERADMIN, ADMIN, MANAGER | same | same | same |
| Stock | SUPERADMIN, ADMIN, MANAGER | same | SUPERADMIN, ADMIN, MANAGER | same |
| Stock transactions | SUPERADMIN, ADMIN, MANAGER | same | same | same |
| Vaccinations | + VETERINARIAN | same | same | (no delete) |
| Inseminations | + VETERINARIAN | same | same | (no delete) |
| Veterinarians | SUPERADMIN, ADMIN, MANAGER | + VETERINARIAN | SUPERADMIN, ADMIN, MANAGER | (no delete) |
| Activity logs | - | SUPERADMIN (all), ADMIN/MANAGER (farm), any (own) | - | - |

\* MANAGER: users for their farm only.

## Frontend

- **Sidebar**: Menu items filtered by `item.roles`; VETERINARIAN sees Dashboard, Cattle, Health, Activity logs (and Profile). ADMIN and MANAGER see Farms, Production, Stock, etc. SUPERADMIN sees all.
- **List pages**: Add / Edit / Delete buttons use `canCreateEntity`, `canUpdateEntity`, `canDeleteEntity` from `@/utils/permissions` so only allowed roles see actions (e.g. Cattle: VETERINARIAN sees list only, no Add/Edit/Delete).
- **Farm detail** (`/account/farms/:farmId`): Available to any role that can access the dashboard. Backend enforces scope: SUPERADMIN can view any farm; ADMIN only farms they own; MANAGER only farms they manage; VETERINARIAN only farms they are assigned to. The **View** button on the Farms list is shown to SUPERADMIN, ADMIN, and MANAGER.
- **Farms list**: SUPERADMIN sees all farms and filters (search, location, status, owner); ADMIN and MANAGER see only their farm(s). Create-farm button and Activate action are SUPERADMIN-only.
- **Protector**: Allows SUPERADMIN, ADMIN, MANAGER, VETERINARIAN to access the dashboard.

Keep `frontend/Difarm-Fe/src/utils/permissions.ts` in sync with backend route roles when changing who can do what.

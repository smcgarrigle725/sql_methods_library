# 23 Database Security

A practical reference for PostgreSQL database security in healthcare and finance contexts. Covers role-based access control, row level security, encryption and masking, audit logging, and SQL injection prevention. All notebooks run live with SQLite + Python stdlib, with complete PostgreSQL DDL and patterns throughout.

## Dataset (healthcare/finance theme)

Five tables representing a provincial health authority system:

- **patients** — 5 records; full_name, dob, health_card (sensitive PII), phone, province
- **staff** — 5 records; dr_lee/dr_pham (clinicians ON/BC), nurse_ann, billing_jd, admin_sue
- **clinical_notes** — 5 notes with sensitivity levels: normal, sensitive, restricted
- **financial_accounts** — 5 accounts across insurance/billing/payment types
- **audit_log** — populated during notebook demos with INSERT/UPDATE/SELECT events

## Notebooks

| Notebook | Topics covered |
|---|---|
| `roles_and_privileges` | Role type hierarchy (LOGIN, group, superuser, INHERIT, SET ROLE); healthcare role design (clinician_role, nurse_role, billing_role, app_role); GRANT/REVOKE reference (table, schema, column-level, EXECUTE, future tables via ALTER DEFAULT PRIVILEGES); column-level privilege demo showing billing sees patient_id/full_name/province but not health_card; least-privilege matrix across 6 roles and 4 tables; privilege audit queries (information_schema.role_table_grants, pg_auth_members, pg_roles) |
| `row_level_security` | RLS concept (database-enforced WHERE clause); ENABLE/FORCE ROW SECURITY DDL; province isolation — each staff member sees only their province's patients; sensitivity levels (normal/sensitive/restricted) — nurse sees normal+sensitive, clinician sees all, billing sees none; USING vs WITH CHECK for read vs write policies; PERMISSIVE vs RESTRICTIVE; current_setting() for app context passing; policy audit via pg_policies |
| `encryption_and_masking` | Salted SHA-256 hashing with per-record salts (secrets.token_hex); timing-safe comparison with hmac.compare_digest; same-value different-salt demo proving hash inequality; pgcrypto crypt()/gen_salt('bf') bcrypt pattern; masking functions (health_card last-4, phone ***-***-XXXX, DOB year-only, partial name); masked view pattern (GRANT on view, REVOKE on base table); column-level XOR encryption demo illustrating key management; pgcrypto pgp_sym_encrypt/decrypt; encryption strategy comparison (TDE, column pgcrypto, app-layer AES, hashing) |
| `audit_logging` | log_event() Python function simulating trigger captures; full workflow: chart SELECT, note INSERT, balance UPDATE, staff deactivation; audit_log schema (log_id, event_time, username, action, table_name, record_id, old_values JSON, new_values JSON, client_ip, query_hash); complete PostgreSQL AFTER INSERT OR UPDATE OR DELETE trigger with row_to_json(OLD)/row_to_json(NEW); current_setting('app.current_username') for app identity; inet_client_addr(), pg_backend_pid(), current_query(); pgaudit setup (postgresql.conf + per-role config); forensic queries (patient record history, user actions today, balance delta fraud detection, >50 SELECT/hour anomaly) |
| `security_patterns` | SQL injection live demo: UNION attack on VULNERABLE string-interpolated function exfiltrating health_cards; parameterised query blocking same attack; 5 injection types (classic, boolean blind, time-based, stacked queries, second-order); dynamic ORDER BY whitelist pattern; psycopg2 sql.Identifier for safe dynamic identifiers; full 6-category security checklist: Authentication (5 items), Authorisation (5), Encryption (4), Auditing (5), Code/queries (4), Operations (5) |

## Key patterns

```sql
-- RBAC setup:
CREATE ROLE clinician_role NOLOGIN;
GRANT SELECT, INSERT ON clinical_notes TO clinician_role;
GRANT SELECT (patient_id, full_name, province) ON patients TO billing_role;

-- RLS:
ALTER TABLE clinical_notes ENABLE ROW SECURITY;
ALTER TABLE clinical_notes FORCE ROW SECURITY;
CREATE POLICY nurse_sensitivity ON clinical_notes
    FOR SELECT TO nurse_role
    USING (sensitivity IN ('normal', 'sensitive'));

-- Masking view:
CREATE VIEW patients_masked AS
SELECT patient_id, mask_health_card(health_card) AS health_card, province
FROM patients;
GRANT SELECT ON patients_masked TO nurse_role;
REVOKE SELECT ON patients FROM nurse_role;

-- Audit trigger:
CREATE TRIGGER audit_clinical_notes
    AFTER INSERT OR UPDATE OR DELETE ON clinical_notes
    FOR EACH ROW EXECUTE FUNCTION audit_trigger_fn();

-- Safe query (parameterised):
cursor.execute("SELECT * FROM patients WHERE province = %s", (user_input,))
```

## Canadian compliance context

- **PHIPA** (Personal Health Information Protection Act, Ontario) — governs health records; requires access controls, audit logs, breach notification
- **PIPEDA** (Personal Information Protection and Electronic Documents Act) — federal PII protection; applies to financial data
- **PCI-DSS** — payment card data; requires encryption, audit trails, access controls
- All patterns in this folder align with these frameworks

## Dependencies

```
python >= 3.8
jupyter
sqlite3  (stdlib — all notebooks fully runnable without PostgreSQL)
# PostgreSQL patterns: PostgreSQL 12+ for GENERATED ALWAYS AS, RLS
# PostgreSQL 9.5+ for CREATE POLICY / ALTER TABLE ENABLE ROW SECURITY
# pgcrypto: CREATE EXTENSION pgcrypto (included in standard PostgreSQL distributions)
# pgaudit: separate installation — see https://github.com/pgaudit/pgaudit
```

---
*sql_methods_library - Samantha McGarrigle*

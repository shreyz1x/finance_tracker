# Events and Dues: Database Mapping (DRAFT)

Single chapter (UTD). No chapters table, no chapter_id anywhere.

| Table | Columns |
|---|---|
| executive_memberships | user_id (PK, auth user), is_active, created_at |
| chapter_members (people who owe dues, NO app login) | id, full_name, email (optional), is_active, created_by, created_at |
| events | id, name, event_date, description (optional), created_by, created_at |
| dues_assessments | id, member_id, term, amount_cents, created_by, created_at |
| transactions | id, created_by, type, amount_cents, transaction_date, category, description (optional), event_id (optional), dues_assessment_id (optional), created_at |

## Rules enforced by the database
- amounts are whole cents, greater than 0
- type is income or expense
- one assessment per member per term (unique member_id + term)
- income cannot link to an event; expense cannot link to a dues assessment
- event_id / dues_assessment_id must point to real rows (foreign keys)
- only active executives can read or insert anything
- no UPDATE or DELETE on any table from the app this sprint
- dues income can ONLY be created through the function record_dues_payment

## For CS2: recording dues
supabase.rpc('record_dues_payment', {
  p_assessment_id, p_amount_cents, p_transaction_date, p_description
})
Returns the saved transaction row, or an error (overpayment, not authorized,
assessment not found). Do not insert dues income directly; it is blocked.
Query as the logged-in user. Never use the service_role key in app code.

## Open questions
- Does the contract use different column names? (list differences here)
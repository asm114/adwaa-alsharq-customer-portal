# نسخة أمان قبل تنظيف Supabase

المشروع: `ztqqdjryvecscidxxbfe`

تاريخ التصدير: `2026-08-01T15:20:32Z`

الغرض: حفظ حالة بوابة العملاء قبل فصل صلاحيات الإدارة عن `resort_admins` وتنظيف مكونات النظام الأساسي القديمة.

## عدد الصفوف

| الجدول | عدد الصفوف |
| --- | ---: |
| `customer_portal_activity_log` | 0 |
| `customer_portal_contact` | 1 |
| `customer_portal_feedback` | 0 |
| `customer_portal_images` | 2 |
| `customer_portal_pricing` | 1 |
| `customer_portal_resort_info` | 1 |
| `customer_portal_seasons` | 0 |
| `customer_portal_unavailable_periods` | 1 |
| `customer_portal_visitor_counter` | 1 |
| `resort_admins` | 1 |

## الدوال المحفوظة

### دوال بوابة العملاء

- `public.begin_customer_portal_feedback(p_visitor_key text, p_category text, p_message text, p_customer_name text, p_contact_number text)`
- `public.finalize_customer_portal_feedback(p_feedback_id uuid, p_upload_token text, p_image_paths text[])`
- `public.increment_customer_portal_visitor(p_visitor_key text)`
- `public.is_resort_admin()`
- `public.log_customer_portal_admin_change()`
- `public.set_customer_portal_contact_updated_at()`
- `public.set_customer_portal_images_updated_at()`
- `public.set_customer_portal_pricing_updated_at()`
- `public.set_customer_portal_resort_info_updated_at()`
- `public.set_customer_portal_seasons_updated_at()`
- `public.set_customer_portal_unavailable_periods_updated_at()`
- `private.can_upload_customer_portal_feedback(p_name text)`

### دوال قديمة مرتبطة بالنظام الأساسي

- `public.check_resort_booking_overlap()`
- `public.check_unavailable_period_overlap()`
- `public.get_resort_availability(p_from date, p_to date)`
- `public.log_resort_change()`
- `public.log_resort_price_change()`
- `public.cleaner_get_task(p_task_id uuid, p_token text)`
- `public.cleaner_sanitize_issues(p_items jsonb, p_strict boolean)`
- `public.cleaner_sanitize_photos(p_items jsonb, p_strict boolean)`
- `public.cleaner_update_task(p_task_id uuid, p_token text, p_expected_revision bigint, p_patch jsonb)`

## Buckets وStorage metadata

| Bucket | عام؟ | الحد | الأنواع |
| --- | --- | ---: | --- |
| `customer-portal-images` | نعم | 10MB | `image/jpeg`, `image/png`, `image/webp` |
| `customer-portal-feedback` | لا | 5MB | `image/jpeg`, `image/png`, `image/webp` |

ملفات Storage المحفوظة في النسخة:

- `customer-portal-images/portal/20260731201203797-y25q3v-img-0053-png.webp`
- `customer-portal-images/portal/20260731202447345-6ohwsq-img-0054-png.webp`

## السياسات والتريغرز

تم فحص سياسات RLS والتريغرز من `public` وStorage قبل التنظيف. السياسات النشطة الخاصة بالبوابة مبنية على:

- قراءة عامة لبيانات العرض المسموحة.
- تعديل المدير عبر `is_resort_admin()`.
- إرسال الملاحظات عبر RPC محدود.
- إدارة Storage للمدير، ورفع مرفقات الملاحظات عبر مسار مقيد.

سيتم نقل مرجع `is_resort_admin()` من `resort_admins` إلى `customer_portal_admins` قبل حذف أي جدول قديم.

### أسماء سياسات البوابة المحفوظة

- `admins create customer portal activity log`
- `admins read customer portal activity log`
- `admins insert customer portal contact`
- `admins update customer portal contact`
- `public reads customer portal contact`
- `admins delete customer portal feedback`
- `admins read customer portal feedback`
- `admins update customer portal feedback`
- `admins delete customer portal images`
- `admins insert customer portal images`
- `admins read all customer portal images`
- `admins update customer portal images`
- `public reads visible customer portal images`
- `admins insert customer portal pricing`
- `admins update customer portal pricing`
- `public reads customer portal pricing`
- `admins insert customer portal resort info`
- `admins update customer portal resort info`
- `public reads customer portal resort info`
- `admins delete customer portal seasons`
- `admins insert customer portal seasons`
- `admins read all customer portal seasons`
- `admins update customer portal seasons`
- `public reads active customer portal seasons`
- `admins delete customer portal unavailable periods`
- `admins insert customer portal unavailable periods`
- `admins update customer portal unavailable periods`
- `public reads customer portal unavailable periods`
- `admins read customer portal visitor counter`
- `admins upload customer portal image files`
- `admins update customer portal image files`
- `admins delete customer portal image files`
- `admins read customer portal feedback images`
- `admins delete customer portal feedback images`
- `visitors upload customer portal feedback images`
- `visitors clean failed customer portal feedback uploads`

### أسماء التريغرز المحفوظة

- `customer_portal_contact_set_updated_at`
- `customer_portal_images_set_updated_at`
- `customer_portal_pricing_set_updated_at`
- `customer_portal_resort_info_set_updated_at`
- `customer_portal_seasons_set_updated_at`
- `customer_portal_unavailable_periods_set_updated_at`
- `log_customer_portal_contact`
- `log_customer_portal_feedback`
- `log_customer_portal_images`
- `log_customer_portal_pricing`
- `log_customer_portal_resort_info`
- `log_customer_portal_seasons`
- `log_customer_portal_unavailable_periods`
- `log_resort_bookings`
- `resort_booking_overlap_guard`
- `log_resort_prices`
- `resort_unavailable_overlap_guard`

## حالة النسخة

نجحت النسخة الاحتياطية وتحتوي:

- بيانات جداول `customer_portal_*`.
- سجل المدير الحالي من `resort_admins`.
- بيانات Storage metadata للـBuckets الخاصة بالبوابة.
- أسماء الدوال والسياسات والتريغرز ذات الصلة.

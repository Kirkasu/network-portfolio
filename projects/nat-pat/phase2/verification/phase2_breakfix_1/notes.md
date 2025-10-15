Cause: ip nat outside missing on g0/1
Symptom: No translations; outbound pings fail
Fix: Reapply "ip nat outside" + clear translations

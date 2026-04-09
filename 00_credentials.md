# Elysium Technologies Corp — Penetration Test Report

---
## All Credentials
ubuntu system: `root` & `asdf123`

| Service            | URL                                       | Username              | Password        | Notes                              |
| ------------------ | ----------------------------------------- | --------------------- | --------------- | ---------------------------------- |
| **Corporate Site** | http://elysium.futr                       | —                     | —               | Public, no auth                    |
| **Blog Admin**     | http://blog.elysium.futr/wp-admin         | `eladmin`             | `Ely$ium2024!`  | WordPress admin panel              |
| **Shop Admin**     | http://shop.elysium.futr/login.php        | `shopadmin`           | `Sh0p@dmin99`   | Also: any user via SQLi            |
| **Shop Customer**  | http://shop.elysium.futr/login.php        | `alice.wong`          | `Alice2024!`    | Regular customer account           |
| **HR Admin**       | http://hr.elysium.futr/login              | `hradmin`             | `HrAdm1n!`      | Full HR admin access               |
| **HR Employee**    | http://hr.elysium.futr/login              | `john.doe`            | `Hr@2024Pass`   | Regular employee account           |
| **HR Employee 2**  | http://hr.elysium.futr/login              | `sarah.k`             | `SarahK2024!`   | Marketing department               |
| **API**            | http://api.elysium.futr/api/v1/auth/login | `n.kerr@elysium.futr` | `APIdev2024#`   | JWT token auth                     |
| **API Admin**      | http://api.elysium.futr/api/v1/auth/login | `dev@elysium.futr`    | `APIdev2024#`   | Internal admin user                |
| **Gitea Admin**    | http://git.elysium.futr                   | `gitadmin`            | `G1t@Admin24`   | Internal git hosting               |
| **Mail Admin**     | http://mail.elysium.futr                  | `admin@elysium.futr`  | `M@1l4dmin!`    | Roundcube webmail                  |
| **Support Staff**  | http://support.elysium.futr/login.php     | `staff`               | `Support123!`   | Helpdesk staff account             |
| **Support Admin**  | http://support.elysium.futr/login.php     | `admin`               | `Supp0rt@dm`    | Helpdesk admin — ticket mgmt       |
| **Grafana**        | http://monitor.elysium.futr               | `admin`               | `admin`         | ⚠ Default creds — intentional vuln |
| **Adminer**        | http://dev.elysium.futr                   | *(no auth)*           | —               | ⚠ No auth gate — intentional vuln  |
| **VPN Portal**     | http://vpn.elysium.futr                   | `vpnadmin`            | `VPN@Adm2024`   | VPN config download                |
| **MySQL Root**     | tcp://mysql:3306 (internal)               | `root`                | `R00t@Elysium!` | Accessible via dev.elysium.futr    |
| **WordPress DB**   | (internal)                                | `wp_user`             | `WpPass2024!`   | DB: wordpress                      |
| **Shop DB**        | (internal)                                | `shop_user`           | `Sh0pDB2024!`   | DB: shop_db                        |
| **Support DB**     | (internal)                                | `support_user`        | `Support123!`   | DB: support_db                     |
| **MongoDB**        | tcp://mongo:27017 (internal)              | `apiuser`             | `M0ng0@2024!`   | DB: elysium_api                    |
| **VM SSH**         | ssh://`<LAB_IP>`:22                       | `elysium`             | `Ely$iumL4b!`   | Docker host shell                  |

---
## Vulnerability Summary

| #   | Name                               | Subdomain | Severity    | CWE      |
| --- | ---------------------------------- | --------- | ----------- | -------- |
| 1   | SQL Injection — Auth Bypass        | shop      | 🔴 Critical | CWE-89   |
| 2   | BOLA / IDOR                        | api       | 🔴 Critical | CWE-639  |
| 3   | RCE via File Upload                | blog      | 🟠 High     | CWE-434  |
| 4   | Server-Side Template Injection     | hr        | 🟠 High     | CWE-1336 |
| 5   | Hardcoded Credentials in Git Repo  | git       | 🟠 High     | CWE-312  |
| 6   | Cross-Site Request Forgery         | support   | 🟠 High     | CWE-352  |
| 7   | Missing Authentication             | dev       | 🟠 High     | CWE-306  |
| 8   | Stored XSS                         | mail      | 🟠 High     | CWE-79   |
| 9   | Default Credentials                | monitor   | 🟡 Medium   | CWE-1392 |
| 10  | JWT Algorithm Confusion (alg:none) | api       | 🟡 Medium   | CWE-345  |
| 11  | IDOR — Order Enumeration           | shop      | 🟡 Medium   | CWE-639  |
| 12  | Broken Access Control — Priv Esc   | hr        | 🟡 Medium   | CWE-269  |
| 13  | XML External Entity Injection      | blog      | 🟡 Medium   | CWE-611  |
| 14  | Blind Time-Based SQL Injection     | support   | 🟡 Medium   | CWE-89   |
| 15  | Information Disclosure via Errors  | vpn       | 🟢 Low      | CWE-209  |

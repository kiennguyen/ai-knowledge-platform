ở Việt Nam hiện rất phân mảnh, không có 1 chuẩn duy nhất toàn quốc.



Nhưng thực tế triển khai thường rơi vào vài kiểu này:



1\. Phổ biến nhất: Active Directory + LDAP



Rất nhiều:



UBND

sở ban ngành

cơ quan hành chính

trường học công

bệnh viện công



đang dùng:



Microsoft Active Directory

LDAP

Windows Domain



để quản lý:



user

máy tính

permissions

nội bộ LAN



Đây là mô hình lâu đời và cực phổ biến.



2\. Một số nơi mới hơn dùng SSO/OIDC/SAML



Các hệ thống mới hơn sẽ có:



SSO portal

IAM server

OAuth2

OpenID Connect (OIDC)

SAML



Thường build bằng:



Keycloak

Azure AD / Microsoft Entra

Viettel IAM nội bộ

VNPT SSO

custom IAM



OIDC/OAuth2/SAML hiện là chuẩn enterprise phổ biến.



3\. Rất nhiều hệ thống vẫn… login bằng database riêng



Đây là sự thật ở VN.



Nhiều phần mềm hành chính:



mỗi app 1 login riêng

không SSO

không OAuth

không LDAP

password lưu local DB



=> cực legacy.



Quan trọng nhất cho bạn:

ĐỪNG assume họ có JWT/OIDC sẵn



Nhiều nơi KHÔNG có.



Bạn phải thiết kế:



flexible auth integration

enterprise auth adapters

Kiến trúc đúng cho AI Knowledge OS



Bạn nên support:



Auth Type	Priority

Local JWT	MUST

LDAP	MUST

Active Directory	MUST

OIDC/OAuth2	MUST

SAML	SHOULD

API Key	SHOULD

PKI/USB token	Optional

Government CA integration	Future

Thực tế triển khai sẽ là

Option 1 — Internal auth



Bạn quản lý:



user

password

JWT



Đơn giản nhất cho SME.



Option 2 — LDAP/AD integration



Doanh nghiệp đã có:



AD server

Windows domain



Flow:



User login

&#x20; ↓

AI Knowledge OS

&#x20; ↓

LDAP bind

&#x20; ↓

AD authenticate

&#x20; ↓

Generate internal JWT



Tức là:



LDAP/AD chỉ để verify user

hệ thống của bạn vẫn issue JWT riêng



Đây là cách chuẩn enterprise.



Bạn KHÔNG dùng trực tiếp LDAP token



Bạn nên:



External auth



↓



Internal JWT session

Kiến trúc enterprise chuẩn thường là

AD / LDAP / Keycloak / Azure AD

&#x20;           ↓

&#x20;     Identity Provider

&#x20;           ↓

&#x20;     AI Knowledge OS

&#x20;           ↓

&#x20;    Internal JWT + RBAC

Nếu build production-ready



Tôi khuyên:



Phase 1



Support:



email/password

JWT

refresh token

Phase 2



Add:



LDAP

Active Directory

Keycloak OIDC



Đây là 80% enterprise cases.



Phase 3



Add:



SAML

Azure AD

Government IAM integration

Tôi nghĩ stack auth tốt cho bạn là

Identity layer



Dùng:



Keycloak



hoặc

Authentik



rồi integrate:



LDAP

AD

OIDC

SAML



Keycloak support:



LDAP federation

Active Directory

OAuth2

OIDC

SAML2

Vì sao Keycloak hợp bài này?



Vì enterprise Việt:



nhiều hệ thống cũ

auth rất lộn xộn

không standardized



Keycloak giúp:



bridge các hệ auth

centralized IAM

SSO

RBAC

federation

Kiến trúc rất hợp cho AI Knowledge OS

Enterprise AD/LDAP

&#x20;       ↓

&#x20;    Keycloak

&#x20;       ↓

AI Knowledge OS API Gateway

&#x20;       ↓

Internal JWT

&#x20;       ↓

Microservices

Cực kỳ quan trọng



Trong enterprise AI:



Auth không phải phần khó nhất



Mà là:



permission mapping

document ACL

retrieval security



Ví dụ:



user login được

nhưng AI không được leak tài liệu phòng nhân sự



Đây mới là bài toán khó.



Tôi đánh giá thật



Nếu bạn support tốt:



LDAP

AD

OIDC

Keycloak



thì đã đủ ăn phần lớn thị trường enterprise VN rồi.



Đừng lo chuyện:



“họ dùng JWT gì?”



Vì cuối cùng:



bạn vẫn nên issue internal JWT riêng

internal RBAC riêng

internal permission graph riêng



để AI layer kiểm soát retrieval/security tốt hơn.


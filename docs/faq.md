# FAQ

This page captures common questions raised during the MyAccessID Online Workshop on 2 December 2025.

### Q1. What is MyAccessID? {#q1-what-is-myaccessid} 
[MyAccessID](https://wiki.geant.org/spaces/MyAccessID/overview) is a federated identity service operated by [GÉANT](https://geant.org/) that lets users log in to computing services by Digital Research Infrastructures (DRI) providers using their home institution credentials (via eduGAIN).  

For service providers, it removes the need to operate a login system or manage separate guest accounts. For users, it means a single, trusted login that works across supported research services providers. 

---

### Q2. How can I check if my institution supports MyAccessID? {#q2-check-institution} 
You can check by visiting one of these attribute checker tools: 

- **[https://myaccessid.devtest.eduteams.org/sp/](https://myaccessid.devtest.eduteams.org/sp/)**
- **[https://mms.myaccessid.org/fed-apps/profile/](https://mms.myaccessid.org/fed-apps/profile/)** 

Log in using your university credentials and review the attributes (name, surname, email) released. If you can log in successfully and see meaningful attributes, your institution is likely already participating. 

---

### Q3. When and why should a service provider use MyAccessID? {#q3-when-and-why} 
DRI providers often face challenges managing user identities for users coming from across institutions on their systems. Also, if local identities are created, this may lead to unclear affiliations, manual authorisation, offboarding challenges and GDPR hurdles. MyAccessID addresses many of these by offering: 

- **A compliant and consistent identity layer over eduGAIN**
- **Simplified OIDC-based integration**
- **Enforced minimum attribute profiles**  
- **A way to avoid running a local IdP or managing guest accounts manually**

It’s especially useful for services like HPC portals, storage platforms, and other shared national or international infrastructures. 

---

### Q4. Which UK sites are currently using MyAccessID for identity federation, and why? {#q4-curremt-UK-sites} 
MyAccessID is already in production use at the following UK research computing sites: 

- **University of Bristol: AIRR Service (Isambard-AI system) and Isambard-3 Service**
- **University of Cambridge: AIRR Service (Dawn system)**
- **UCL and STFC Hartree Centre – Evaluation and early rollout** 

These sites adopted MyAccessID because it offered a ready-to-use, standards-compliant, low-friction way to onboard external users from multiple institutions without building or operating bespoke identity infrastructure. 

---

### Q5. What is the key value of using MyAccessID over a direct eduGAIN connection? {#q5-MyAccessID-over-eduGAIN}
eduGAIN enables federation but does not enforce how identities or attributes are released. MyAccessID adds a managed layer that: 

- **Normalises and validates attribute release**
- **Enforces usable identity profiles**
- **Simplifies service integration**
- **Provides shared assurance around GDPR and acceptable use**

This significantly reduces operational burden for service providers. 

### Q6. Does eduGAIN provide a minimum attribute set for identity assurance? {#q6-MyAccessID-over-eduGAIN}
No. eduGAIN leaves attribute release to individual institutions, and many UK universities release only opaque IDs or no consistent attributes. MyAccessID enforces a usable minimum set and checks for completeness. 

### Q7. Can a service provider connect directly to eduGAIN instead of using MyAccessID? {#q7-MyAccessID-over-eduGAIN}
Technically yes but doing so requires significant effort in configuring trust anchors, handling attribute variability, and maintaining ongoing compliance. MyAccessID offers a managed alternative that abstracts away much of this complexity and is already widely adopted by EuroHPC.

---

### Q8. Who should I contact if I hit attribute mismatch or login issues? {#q8-MyAccessID-attribute-mismatch}
First, use the attribute checkers listed in Q2 to confirm what your institution is releasing. If the attributes are correct, then contact MyAccessID IAM Service Team at support@myaccessid.org. 

They can help diagnose whether your institution is releasing attributes correctly and support troubleshooting with DRI service providers. 

### Q9. How are incident response and user trust handled in a federated model like MyAccessID? {#q9-MyAccessID-incident-response-trust}
Incident response begins with the project PI or the DRI service provider. If a user is flagged (e.g. for misuse or policy breaches), the issue is escalated back to the user’s home institution and/or MyAccessID. Providers have monitoring and logging and can temporarily or permanently block access while maintaining forensic trails. 

### Q10. What role could GOV.UK One Login, eIDAS, or other national eIDs play in MyAccessID's future? 
This remains exploratory. 

---

### Q11. Is MyAccessID compliant with UK regulatory and security frameworks like NIST or CAF? {#q11-MyAccessID-compliance}
Yes. The teams involved in deploying MyAccessID (e.g. Bristol, Cambridge) have gone through CAF assessments, including threat modelling and incident response planning. MyAccessID aligns with ISO 27001 and provides contracts and documentation to satisfy legal reviews at institutions.

### Q12. How does MyAccessID handle authorisation beyond just login (authentication)? {#q12-MyAccessID-authentication-authorisation}
No. MyAccessID is responsible for authentication and identity assurance only. Authorisation remains the responsibility of the DRI providers. 

### Q13. Can MyAccessID integrate with systems like SAFE for resource management? {#q13-MyAccessID-integration}
Yes, MyAccessID can be linked to platforms like SAFE to provide identity federation but still needs to be explored based on the use case scenario.  

### Q14. What support does Jisc provide in resolving identity or attribute mismatches? {#q14-MyAccessID-jisc}
At present, Jisc is not providing operational support for MyAccessID. This is an active topic of discussion within the community. 

---

### Q15. Can MyAccessID support users from industry or organisations without an IdP? {#q15-MyAccessID-industry-users} 
Currently NO. Options include: 

- **An "IdP of last resort" for users not covered by institutional IdPs**
- **Email-based authentication using magic links**

---

What’s next? Will there be more workshops? 

Yes. The MyAccessID community is planning additional workshops. Details will be shared via the [IAM4DRI mailing list](https://www.jiscmail.ac.uk/cgi-bin/wa-jisc.exe?SUBED1=IAM4DRI&A=1) and the official site. Stay tuned!  

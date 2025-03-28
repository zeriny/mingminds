#PKI知识库

## Short-lived Certificate

《Short-Lived Certificates Are Coming in 2025》https://www.feistyduck.com/newsletter/issue_120_short-lived_certificates_are_coming_in_2025

- We’ve known for a while that Google wants to reduce certificate lifetimes to ninety days, but earlier this year, [Apple surprised everyone](https://www.feistyduck.com/newsletter/issue_118_apple_wants_to_limit_certificates_to_forty_five_days) by pushing for as little as **forty-five** days (forty-seven in the latest proposal)
- In his [end-of-year letter](https://letsencrypt.org/2024/12/11/eoy-letter-2024/), Josh Aas, the founder and executive director of the Internet Security Research Group (ISRG), announced plans for six-day certificates. *Happy 2025: short-life certificates for everyone!*

《Apple Wants to Limit Certificates to Forty-Five Days》https://www.feistyduck.com/newsletter/issue_118_apple_wants_to_limit_certificates_to_forty_five_days

《A Note from our Executive Director》https://letsencrypt.org/2024/12/11/eoy-letter-2024/

- Our longstanding offering won’t fundamentally change next year, but we are going to introduce a new offering that’s a big shift from anything we’ve done before - **short-lived certificates**. Specifically, certificates with **a lifetime of six days**. This is a big upgrade for the security of the TLS ecosystem because it minimizes exposure time during a key compromise event.



## Certificate Information

- 证书有效期越来越短，更新频率激增；在一些IoT等对性能要求比较高的场景，证书文件过大会影响性能。因此能否考虑将证书信息简化？
---
layout: page
title: Crictl
menubar: cheatsheets_menu
hide_footer: true
show_sidebar: false
toc: true
---

<img align="center" width="70%" src="../../images/crio-logo.png">

## Generic

| ```crictl -D pull --creds <USER>:<PASSWORD> docker.io/istio/proxyv2:1.14.1``` |

## Authenticate daemon

```
# podman login -u testuser -p testpassword docker.io
# sudo cp ${XDG_RUNTIME_DIR}/containers/auth.json /etc/crio/
# cat /etc/crio/auth.json 
{
    "auths": {
        "docker.io": {
                        "auth": "amF2aXJvbWFuOnBdGhvbjFiaWNoQRvY2tlcmh1Qg=="
                
        }
        
    }

}

# grep global_auth_file /etc/crio/crio.conf 
global_auth_file = "/etc/crio/auth.json"

# sudo systemctl restart crio
```


<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/vertical-privilege-escalation/main/content/vertical-privilege-escalation.svg"></p>

A threat actor may perform unauthorized functions belonging to another user with a higher privilege level.

Clone this current repo recursively
```sh
git clone --recursive https://github.com/qeeqbox/horizontal-privilege-escalation
```
Run the webapp using Python
```sh
python3 horizontal-privilege-escalation/vulnerable-web-app/webapp.py
```
Open the webapp in your browser 127.0.0.1:5142
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/vertical-privilege-escalation/main/content/1.png"></p>
Login as John (username: john and password: john)
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/vertical-privilege-escalation/main/content/2.png"></p>
John has access to the tickets only
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/vertical-privilege-escalation/main/content/3.png"></p>
Logout
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/vertical-privilege-escalation/main/content/4.png"></p>
Login as Joe (username: joe and password: joe)
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/vertical-privilege-escalation/main/content/5.png"></p>
Joe also has access to the tickets and sysinfo
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/vertical-privilege-escalation/main/content/6.png"></p>

## Code
This logic checks if the user is logged in, then it renders sections based on the user's access
```py
@logged_in
def render_home_page(self):
    content = b""
    cookies = SimpleCookie(self.headers.get('Cookie'))
    if "access" in cookies:
        for access in cookies["access"].value.split(","):
            content += getattr(self, f"{access}_section" , None)()
    return BASE_TEMPLATE.replace(b"{{body}}",content)
```
 
## Impact
Vary

## Risk
- Session Hijacking
- Credential Theft

## Redemption
- Client input validation
- Output encoding
- Browser built-in XSS preveiton

## ID
cb251c97-067d-4f13-8195-4f918273f41b

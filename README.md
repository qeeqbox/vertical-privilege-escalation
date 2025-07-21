<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/horizontal-privilege-escalation/main/content/horizontal-privilege-escalation.svg"></p>

A threat actor may perform unauthorized functions belonging to another user with a similar privilege level.

Clone this current repo recursively
```sh
git clone --recursive https://github.com/qeeqbox/horizontal-privilege-escalation
```
Run the webapp using Python
```sh
python3 horizontal-privilege-escalation/vulnerable-web-app/webapp.py
```
Open the webapp in your browser 127.0.0.1:5142
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/horizontal-privilege-escalation/main/content/1.png"></p>
Login as John (username: john and password: john)
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/horizontal-privilege-escalation/main/content/2.png"></p>
John has access to the tickets only
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/horizontal-privilege-escalation/main/content/3.png"></p>
Logout
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/horizontal-privilege-escalation/main/content/4.png"></p>
Login as Jane (username: jane and password: jane)
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/horizontal-privilege-escalation/main/content/5.png"></p>
Jane also has access to the tickets only, but her tickets are different
<p align="center"> <img src="https://raw.githubusercontent.com/qeeqbox/horizontal-privilege-escalation/main/content/6.png"></p>

## Code
This logic checks if the user is logged in, then verifies if they have access to the tickets, and finally renders all tickets created by the user
```py
@logged_in
@check_access(access="tickets")
@render_page(file="tickets.html")
def tickets_section(self):
    temp = b""
    with connect(DATABASE, isolation_level=None) as connection:
        results = None
        cursor = connection.cursor()
        cookies = SimpleCookie(self.headers.get('Cookie'))
        if "is_admin" in cookies:
            if cookies['is_admin'].value == "1":
                results = cursor.execute("SELECT * FROM tickets ORDER BY id DESC LIMIT 10").fetchall()
            else:
                results = cursor.execute("SELECT * FROM tickets WHERE username='%s' ORDER BY id DESC LIMIT 10" % self.session["username"]).fetchall()
        else:
            results = cursor.execute("SELECT * FROM tickets WHERE username='%s' ORDER BY id DESC LIMIT 10" % self.session["username"]).fetchall()
        if results:
            for row in reversed(results):
                temp += f"<div>[{row[3]}] {row[1]}: {row[2]}</div>".encode("utf-8")
    return [((b"{{tickets-results}}"),temp)]
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

# Kundendomains hinzufügen

1. [Domains auf unseren Server zeigen lassen](#domains-auf-unseren-server-zeigen-lassen)
2. [Stack im Rancher hinzufügen](#stack-im-rancher-hinzufgen)
3. [Domains im Load Balancer ergänzen](#domains-im-load-balancer-ergnzen)
4. [Zertifikat dem Load Balancer zuweisen](#zertifikat-dem-load-balancer-zuweisen)

### Domains auf unseren Server zeigen lassen

Zeige auf folgende IP-Adresse: `52.29.153.93`.
    
### Stack im Rancher hinzufügen

Füllt [das Formular][1] für den Stack aus. Der `Account Identifier` sollte mit der numerischen Account ID im [Cloud Admin][2] identisch sein, weil dieser demnächst automatisch für die Subdomains unter artbutlercloud.com genutzt wird. Die `Domains` sind ohne Leerzeichen mit Kommata zu trennen. Gebt bitte beide Varianten mit und ohne www an. `Name` und `Description` des Stacks können frei gewählt werden.

![](https://artbutler.s3.amazonaws.com/howto/step2.png)

Mit Klick auf `Launch` wird der Stack gestartet.

### Domains im Load Balancer ergänzen

Fügt je Domain eine Service Rule im [Load Balancer][3] hinzu. Hier auch wieder mit und ohne www. Klicke dazu auf `Add Service Rule`. Nun erscheint am unteren Ende der Liste ein neuer leerer Eintrag.

![](https://artbutler.s3.amazonaws.com/howto/step3.png)

Die Service Rule sollte mit folgenden Attributen und der jeweiligen Domain auf den neu angelegten Let's encrypt Container zeigen. Dieser steht zeit dem letzten Schritt im Dropdown zur Verfügung.

    Access: Public
    Protocol: HTTP
    Request Host: *Domain*
    Port: 80
    Path: /.well-known/acme-challenge
    Target: *Let's encrypt Container*
    Port: 80

![](https://artbutler.s3.amazonaws.com/howto/step4.png)

Mit dem Knopf `Edit` wird die Änderung bestätigt. Dazu ganz nach unten scrollen.

### Zertifikat dem Load Balancer zuweisen

120 Sekunden nachdem der Let's encrypt Conatiner gestartet wurde, probiert er das erste Mal die Domains zu verifizieren. Somit wird das Zertifikat frühestens dann erzeugt. Eventuell musst du dich deshalb noch gedulden. [Beobachte die Liste der Zertifikate][4] gerne, wo das Zertifkat dann automatisch auftaucht. 

![](https://artbutler.s3.amazonaws.com/howto/step5.png)

Ist das Zertifikat aufgetaucht, dann musst du wieder [in den Load Balancer gehen][3] und das Zertifikat anzuhängen. Dazu auf `Add Alternate Certificate` klicken. Nun erscheint wieder am Ende der Liste ein neues Auswahlfeld.

![](https://artbutler.s3.amazonaws.com/howto/step6.png)

Und dann im Dropdown das neue Zertifikat mit dem `Account Identifier` auswählen.

![](https://artbutler.s3.amazonaws.com/howto/step7.png)

Am Ende wieder mit dem Knopf `Edit` bestätigen.

[1]: https://rancher.artbutler.com/env/1a5/catalog/Operations:customer?catalogId=Operations
[2]: https://cloudadmin.artbutler.com
[3]: https://rancher.artbutler.com/env/1a5/apps/add-balancer?serviceId=1s120&stackId=1st5&upgrade=true
[4]: https://rancher.artbutler.com/env/1a5/infra/certificates

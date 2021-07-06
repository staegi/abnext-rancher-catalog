## Kundendomains hinzufügen

1. [Domains auf unseren Server zeigen lassen](#domains-auf-unseren-server-zeigen-lassen)
2. [Stack im Rancher hinzufügen](#stack-im-rancher-hinzufgen)
3. [Domains im Load Balancer ergänzen](#domains-im-load-balancer-ergnzen)
4. [Zertifikat dem Load Balancer zuweisen](#zertifikat-dem-load-balancer-zuweisen)

### Domains auf unseren Server zeigen lassen

telle das A-Record um auf folgende IP-Adresse: `52.29.153.93`. Ob die Umstellung schon aktiv ist, kann man im Terminal (Mac) oder mit Cmd (Windows) mit `ping`, etwa `ping meine-galerie.de`, prüfen.

Bei Kunden, die von vH7 wechseln, um das Zertifikat zu aktualisieren, bitte mit dem [DNS Lookup Tool][5] vorher prüfen, ob in den DNS-Einstellungen ein evtl. vorhandenes AAAA-Record für vH7 (war: `2a01:4f8:191:9050::a7`) wieder gelöscht wurde - es darf bei der Zertifikatsbeantragung kein AAAA-Record mehr eingetragen sein!
    
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

## Weitere Domains für bestehenden Kunden ergänzen

Alle zusätzlichen Domains müssen bereits auf unseren Server (A-Record: 52.29.153.93) zeigen, es darf *kein AAAA-Record* eingetragen sein. Die DNS-Einträge kann man z.B. mit dem [DNS Lookup Tool][5] prüfen, oft reicht auch ein Test im Terminal (Mac) oder mit Cmd (Windows) mit `ping`, etwa `ping meine-galerie.de`.

Dann [wie oben beschrieben](#domains-im-load-balancer-ergnzen) im [Load Balancer][3] die neuen Regeln für die zusätzlichen Domains eintragen und den Let’s Encrypt Container im Kundenstack `Kundenname/letsencrypt` als Target zuweisen.

Danach in der [Liste der Stacks][7] beim Stack des Kunden auf `Up to date` klicken. Im Formular erneut das Template auswählen (Version 1.0.1) und im nächsten Schritt die weiteren Domains ergänzen. Stack mit `Upgrade` sichern. Im letzten Schritt bitte das Update des Stacks mit der gelben Taste `Finish Upgrade` abschliessen.

Der Stack wird aktualisiert und die neuen Domains nach kurzer Zeit (~2 min.) automatisch im bestehenden Zertifikat ergänzt. Dieses muss daher nicht erneut angehängt werden.

## Bei Fehlern

Wenn das Zertifikat nicht erstellt wird, bitte prüfen

* sind die DNS Einstellungen korrekt: [DNS Lookup Tool][5]
* stimmen die eingetragenen Domains und wurde der richtige Stack zugewiesen?

**Achtung**: Die [Rate Limits vom Let’s Encrypt Server][6] erlauben **fünf (5) Anfragen pro Stunde**, wenn also die ersten 5 Versuche wegen eines Fehlers (A-Record falsch oder AAAA-Record vorhanden) in den ersten rund 10 Minuten fehlgeschlagen sind, geht es prinzipiell erst eine Stunde später weiter. Es wird im Abstand von 120 Sekunden geprüft. Das heißt nach 10 Minuten bricht das Script ab und man muss sich dann 50 Minuten gedulden bis zur nächsten Validierung.

## Entfernen von Kunden-Stacks

Wird ein Stack nicht mehr gebraucht, weil der Kunde weg ist und das DNS nicht mehr zu uns auflöst, dann kann man den Stack ganz einfach über das Kontextmenüs des Stacks löschen. ABER VORSICHT, bitte nicht in der Zeile verrutschen ;-)

Falls ihr doch versehentlich einen Stack gelöscht habt, dann kann ich den aber auch wieder via Datenbank herstellen.

![](https://artbutler.s3.eu-central-1.amazonaws.com/howto/removal1.png)

Nun bleibt allerdings noch das vom letsencrypt erzeugte Zertifikat erhalten. Diese findet ihr im Menü unter [INFRASTRUCTURE > Certificates][8]. Das könnt ihr dort auch ganz einfach über das Kontextmenü löschen.

![](https://artbutler.s3.eu-central-1.amazonaws.com/howto/removal2.png)

[1]: https://rancher.artbutler.com/env/1a5/catalog/Operations:customer?catalogId=Operations
[2]: https://cloudadmin.artbutler.com
[3]: https://rancher.artbutler.com/env/1a5/apps/add-balancer?serviceId=1s120&stackId=1st5&upgrade=true
[4]: https://rancher.artbutler.com/env/1a5/infra/certificates
[5]: https://www.ultratools.com/tools/dnsLookup
[6]: https://letsencrypt.org/docs/rate-limits/
[7]: https://rancher.artbutler.com/env/1a5/apps/stacks?which=all
[8]: https://rancher.artbutler.com/env/1a5/infra/certificates

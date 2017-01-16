<h1 align="center">Webentwicklung mit Komponenten</h1>

<p align="center"><b>Vergleich verschiedener Möglichkeiten zur komponenten-gestützten Webentwicklung</b></p>

> Wie können Komponenten für Webanwendungen mit HTML, CSS und JS entwickelt werden und was sollte man bei der Entscheidung für eine Technologie beachten?

## Vorwort

Als Webentwickler stehe ich bei Entwicklungsprojekten immer wieder vor der Herausforderung, mit welcher Struktur eine Internetseite programmiert werden sollte. Genauer stellt sich die Frage, wie das Zusammenspiel von HTML, CSS und JavaScript gehandhabt wird.  
Mit neuen Webtechnologien, besonders durch ständige Neuerungen in JavaScript, werden Internetseiten immer interaktiver. In den letzten Jahren spricht man auch immer öfter von _Webapps_.

Schon bei kleinen Projekten sehe ich das Auseinandersetzen und Festlegen einer Architektur als wichtigen Schritt. Aber vor allem bei größeren und wachsenden Projekten _muss_ ein Entwickler sich damit beschäftigen, welche Technologie/n, Struktur/en und Architektur/en verwendet werden, um eine reibungslose Entwicklung zu gewährleisten.

Eine beliebte Vorgehensweise, um dieses Ziel zu verfolgen, ist das Konzept von Komponenten. Komponenten können mithilfe verschiedener Technologien umgesetzt werden. Dieser Vergleich beschäftigt sich mit jenen Technologien.

Ziel des Vergleichs ist es _nicht_, eine dieser Technologien hervorzuheben und als _richtig_ darzustellen. Vielmehr soll dies dem Leser als **Übersicht** dienen, um einen Überblick über diese Technologien zu haben und als **Hilfe**, um die passende Technologie auszuwählen.

## Über die verschiedenen Wege

Im Folgenden werden drei Wege vorgestellt, mit denen sich Komponenten erstellen lassen. Jeder Weg ist so beschrieben, dass man sich selbst kritisch damit auseinandersetzen kann, um den Weg zu finden, den man **aus subjektiver Sicht, persönlich gerne nutzen möchte.**

Als Programmierer ist mir wichtig, wie der Code aussieht. Wenn ich beispielsweise ein neues Framework entdecke, schaue ich direkt nach Beispielcode. Ist er leicht zu lesen; entsteht viel Boilerplate; ist er selbsterklären; muss man sich oft wiederholen?  
Daher gibt es in jedem Weg Codebeispiele, die verdeutlichen, wie Code aussieht.

Jeder Weg bzw. jede Technologie wird kurz erläutert, wie sie im Hinblick auf Komponentenbildung funktioniert. Dies ist kein vollständiges Tutorial für alle drei Technologien.

Zu jedem Weg werden Hindernisse aufgezeigt, auf die man während dem Programmieren achten muss. Über solche Pitfalls sollte man sich bewusst sein, bevor man eine Technologie auswählt.

Am Ende jeden Kapitels werden Vor- und Nachteile des jeweiligen Wegs genannt. Dies ist auch eine kurze Zusammenfassung der jeweiligen Technologie im Hinblick auf Komponentenbildung.

## Inhaltsverzeichnis

1. [Was sind Komponenten?](01-was-sind-komponenten.md)
2. [Der traditionelle Weg – Decoupling von HTML, CSS und JS](02-der-traditionelle-weg.md)
3. [Der modische Weg – Rendering über JavaScript](03-der-modische-weg.md)
4. [Der native Weg – Web Components als Browsertechnologie](04-der-native-weg.md)
5. Die Antwort

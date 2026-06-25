<!--
author:   OVGU-VET-TechEd
email:    hannes.tegelbeckers@ovgu.de
version:  1.0.0
language: de
narrator: Deutsch Female

title:    Deskriptive Statistik & Korrelation mit R
comment:  Deutschsprachiges Lernmodul zu Verteilungen, Mittel- und Streuwerten sowie Pearson's r
logo:     https://upload.wikimedia.org/wikipedia/commons/thumb/5/5d/OvGU-Logo.svg/1200px-OvGU-Logo.svg.png

link:     https://cdn.jsdelivr.net/chartist.js/latest/chartist.min.css
script:   https://cdn.jsdelivr.net/chartist.js/latest/chartist.min.js
-->

# Deskriptive Statistik & Korrelation mit R

> **Lernziele:**
> Nach diesem Lernmodul können Sie:
>
> - Verteilungen und Verteilungskurven in R visualisieren und interpretieren
> - Lagemaße (Mittelwert, Median, Modus) berechnen und auswählen
> - Streuungsmaße (Varianz, Standardabweichung, IQR) berechnen und verstehen
> - Den Pearson-Korrelationskoeffizient r berechnen und interpretieren
> - Die geeigneten Maße für unterschiedliche Datentypen auswählen

---

**Quellen und weiterführende englische Materialien:**

- [Deskriptive Statistik (EN)](https://liascript.github.io/course/?https://raw.githubusercontent.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/refs/heads/main/descriptive_stats_r_V2.md)
- [Visualisierung (EN)](https://liascript.github.io/course/?https://raw.githubusercontent.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/refs/heads/main/visualization_r_V2.md)
- [Korrelation (EN)](https://liascript.github.io/course/?https://raw.githubusercontent.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/refs/heads/main/correlation_r_V2.md)
- [Normalverteilungscheck (EN)](https://liascript.github.io/course/?https://raw.githubusercontent.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/refs/heads/main/normality_check_r_V2.md)
- [GitHub Repository](https://github.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes)

---

## 🔧 R-Umgebung einrichten

### Schritt 1: Projekt anlegen

```r
# Neues R-Projekt erstellen:
# Datei → Neues Projekt → Neues Verzeichnis → Neues Projekt
# Name: 2025_IhrNachname_Quant_Methoden

# Neues Skript erstellen:
# Datei → Neue Datei → R-Skript
# Speichern als: deskriptive_statistik_ihrnachname.R
```

### Schritt 2: Pakete installieren und laden

```r
# Pakete installieren (nur einmal notwendig)
install.packages("psych")      # Deskriptive Statistik
install.packages("DescTools")  # Zusätzliche Werkzeuge
install.packages("moments")    # Schiefe und Kurtosis
install.packages("ggplot2")    # Visualisierungen
install.packages("corrplot")   # Korrelationsmatrizen

# Pakete laden (bei jeder Sitzung notwendig)
library(psych)
library(DescTools)
library(moments)
library(ggplot2)
library(corrplot)
```

### Schritt 3: Datensatz laden

```r
# Option 1: Eingebauter Datensatz (zum Üben)
data(mtcars)
df <- mtcars

# Option 2: Eigene CSV-Datei laden
# df <- read.csv("ihre_daten.csv")

# Erste Zeilen anzeigen
head(df)

# Struktur prüfen
str(df)

# Überblick über den Datensatz
summary(df)
```

---

## 📊 Teil 1 – Verteilungen und Verteilungskurven

### Was ist eine Verteilung?

{{1}}
Eine **Verteilung** beschreibt, wie häufig verschiedene Werte in einem Datensatz vorkommen. Sie zeigt uns:

- Wo liegen die meisten Werte?
- Wie weit sind die Werte gestreut?
- Gibt es extreme Ausreißer?
- Ist die Verteilung symmetrisch oder schief?

{{2}}
> **Wichtig:** Bevor Sie statistische Tests anwenden, sollten Sie die Verteilung Ihrer Daten immer grafisch überprüfen – „Visualisieren Sie zuerst!"

---

### 📉 Histogramm

Das Histogramm zeigt die Häufigkeitsverteilung numerischer Daten.

```r
# Einfaches Histogramm
hist(df$mpg,
     main  = "Verteilung: Kraftstoffverbrauch (MPG)",
     xlab  = "Meilen pro Gallone",
     ylab  = "Häufigkeit",
     col   = "steelblue",
     border = "white")

# Mittelwert- und Medianlinie hinzufügen
abline(v = mean(df$mpg),   col = "red",  lwd = 2, lty = 2)
abline(v = median(df$mpg), col = "blue", lwd = 2, lty = 2)

# Legende hinzufügen
legend("topright",
       legend = c("Mittelwert", "Median"),
       col    = c("red", "blue"),
       lty    = 2, lwd = 2)
```

**Wann ein Histogramm verwenden?**

- ✅ Kontinuierliche oder diskrete numerische Daten
- ✅ Verteilungsform sichtbar machen
- ✅ Modalität erkennen (ein- oder zweiteilig)
- ✅ Ausreißer oder Lücken aufdecken
- ✅ Normalverteilung optisch prüfen

---

### 🎻 Dichtekurve (Density Plot)

Die Dichtekurve ist eine geglättete Darstellung der Häufigkeitsverteilung.

```r
# Einfache Dichtekurve
plot(density(df$mpg),
     main = "Dichtekurve: Kraftstoffverbrauch",
     xlab = "Meilen pro Gallone",
     ylab = "Dichte",
     col  = "darkblue",
     lwd  = 2)

# Datenpunkte als Striche unterhalb einblenden
rug(df$mpg, col = "red", alpha = 0.5)

# Normalverteilungskurve zum Vergleich hinzufügen
m  <- mean(df$mpg)
s  <- sd(df$mpg)
xv <- seq(min(df$mpg) - 2, max(df$mpg) + 2, length = 200)
lines(xv, dnorm(xv, mean = m, sd = s),
      col = "orange", lwd = 2, lty = 2)

legend("topright",
       legend = c("Beobachtete Dichte", "Normalverteilung"),
       col    = c("darkblue", "orange"),
       lty    = c(1, 2), lwd = 2)
```

**Histogramm + Dichtekurve kombiniert (ggplot2):**

```r
ggplot(df, aes(x = mpg)) +
  geom_histogram(aes(y = ..density..),
                 binwidth = 2,
                 fill = "steelblue", color = "white", alpha = 0.7) +
  geom_density(color = "darkblue", linewidth = 1.2) +
  stat_function(fun = dnorm,
                args = list(mean = mean(df$mpg), sd = sd(df$mpg)),
                color = "orange", linewidth = 1, linetype = "dashed") +
  geom_vline(xintercept = mean(df$mpg),
             color = "red", linetype = "dashed", linewidth = 1) +
  labs(title = "Histogramm mit Dichtekurve",
       subtitle = "Blau = beobachtet | Orange = Normalverteilung",
       x = "Meilen pro Gallone",
       y = "Dichte") +
  theme_minimal()
```

---

### 📦 Boxplot

Der Boxplot zeigt die Verteilung kompakt mit Median, Quartilen und Ausreißern.

```r
# Einfacher Boxplot
boxplot(df$mpg,
        main   = "Boxplot: Kraftstoffverbrauch",
        ylab   = "Meilen pro Gallone",
        col    = "lightgreen",
        border = "darkgreen")

# Boxplot nach Gruppen
boxplot(mpg ~ cyl, data = df,
        main   = "Kraftstoffverbrauch nach Zylinderzahl",
        xlab   = "Anzahl Zylinder",
        ylab   = "Meilen pro Gallone",
        col    = c("lightblue", "lightgreen", "lightyellow"),
        border = "grey30")
```

**Boxplot lesen:**

| Element | Bedeutung |
|---------|-----------|
| Linie in der Box | Median (50. Perzentil) |
| Untere Boxkante | 25. Perzentil (Q1) |
| Obere Boxkante | 75. Perzentil (Q3) |
| Boxhöhe | Interquartilsabstand (IQR = Q3 − Q1) |
| Whisker (Antennen) | Max. 1,5 × IQR |
| Einzelne Punkte | Potenzielle Ausreißer |

---

### 🔔 Normalverteilung prüfen

Die Normalverteilung ist Voraussetzung für viele statistische Tests. So prüfen Sie sie visuell:

**Q-Q-Plot (Quantil-Quantil-Diagramm):**

```r
# Q-Q-Plot erstellen
qqnorm(df$mpg,
       main = "Q-Q-Plot: Kraftstoffverbrauch",
       xlab = "Theoretische Quantile",
       ylab = "Stichproben-Quantile",
       pch  = 19, col = "steelblue")

# Referenzlinie hinzufügen
qqline(df$mpg, col = "red", lwd = 2)
```

> **Interpretation des Q-Q-Plots:**
>
> - **Punkte liegen auf der Linie** → Daten sind normalverteilt ✅
> - **S-förmige Kurve** → Daten sind schief verteilt
> - **Punkte weichen an den Enden ab** → schwere Ränder (Ausreißer)

**Shapiro-Wilk-Test (statistischer Normalverteilungstest):**

```r
# Shapiro-Wilk-Test
shapiro.test(df$mpg)

# Interpretation:
# p > 0.05 → kein signifikanter Unterschied zur Normalverteilung
# p ≤ 0.05 → signifikante Abweichung von der Normalverteilung
```

> ⚠️ **Hinweis:** Bei großen Stichproben (N > 100) reagiert der Shapiro-Wilk-Test sehr empfindlich. Vertrauen Sie in solchen Fällen lieber dem Q-Q-Plot und dem Histogramm.

---

### 📐 Schiefe und Wölbung (Kurtosis)

```r
library(moments)

# Schiefe berechnen
skewness(df$mpg)

# Wölbung berechnen
kurtosis(df$mpg)
```

**Schiefe interpretieren:**

| Wert | Bedeutung |
|------|-----------|
| ≈ 0 | Symmetrische Verteilung (normalverteilt) |
| > 0 | Rechtsschiefe (langer rechter Rand) – Mittelwert > Median |
| < 0 | Linksschiefe (langer linker Rand) – Mittelwert < Median |
| \|Schiefe\| > 1 | Stark schief |

**Wölbung (Kurtosis) interpretieren:**

| Wert | Bezeichnung | Bedeutung |
|------|-------------|-----------|
| ≈ 3 | mesokurtisch | Normal, wie Normalverteilung |
| > 3 | leptokurtisch | Spitz, schwere Ränder, mehr Ausreißer |
| < 3 | platykurtisch | Flach, leichte Ränder, weniger Ausreißer |

---

### ✅ Wissenstest: Verteilungen

**Frage 1:** Ein Histogramm zeigt, dass die meisten Werte auf der linken Seite liegen und der rechte Rand sehr lang ist. Welche Aussage ist korrekt?

```
[( )] Die Verteilung ist linksschiefe.
[(X)] Die Verteilung ist rechtsschiefe (positiv schief).
[( )] Die Verteilung ist normalverteilt.
[( )] Der Median ist größer als der Mittelwert.
[[?]] **Erklärung:** Wenn die meisten Werte links liegen und der Rand rechts lang ist, handelt es sich um eine Rechtsschiefer (positiver Schiefe). In diesem Fall ist der Mittelwert größer als der Median, weil die hohen Werte ihn nach rechts ziehen.
```

---

**Frage 2:** Im Q-Q-Plot folgen die Punkte gut der roten Referenzlinie. Was bedeutet das?

```
[(X)] Die Daten sind annähernd normalverteilt.
[( )] Die Daten haben viele Ausreißer.
[( )] Die Daten sind stark rechtsschiefe.
[( )] Der Shapiro-Wilk-Test wäre signifikant.
[[?]] **Erklärung:** Wenn die Punkte im Q-Q-Plot der Referenzlinie folgen, bedeutet dies, dass die beobachteten Quantile mit den theoretischen Quantilen der Normalverteilung übereinstimmen – die Daten sind also annähernd normalverteilt.
```

---

## 📏 Teil 2 – Mittel- und Streuwerte

### Überblick: Lagemaße und Streuungsmaße

{{1}}
**Lagemaße** (Maße der zentralen Tendenz) beschreiben, wo sich das Zentrum der Daten befindet:

- **Mittelwert (Mean):** arithmetisches Mittel aller Werte
- **Median:** mittlerer Wert bei geordneter Datenmenge
- **Modus:** häufigster Wert

{{2}}
**Streuungsmaße** beschreiben, wie weit die Werte um das Zentrum streuen:

- **Spannweite (Range):** Maximum minus Minimum
- **Varianz:** mittlere quadratische Abweichung vom Mittelwert
- **Standardabweichung (SD):** Wurzel aus der Varianz (in Originaleinheiten)
- **Interquartilsabstand (IQR):** Bereich der mittleren 50 % der Daten

---

### 📊 Lagemaße in R

#### Mittelwert

```r
# Mittelwert berechnen
mean(df$mpg)

# Mittelwert ohne fehlende Werte
mean(df$mpg, na.rm = TRUE)
```

**Wann Mittelwert verwenden?**

- ✅ Daten sind annähernd normalverteilt
- ✅ Keine extremen Ausreißer vorhanden
- ✅ Intervall- oder Verhältnisskala
- ❌ **Nicht geeignet bei:** stark schiefen Daten oder Ausreißern

---

#### Median

```r
# Median berechnen
median(df$mpg)

# Vergleich: Ausreißer beeinflusst den Mittelwert, nicht den Median
beispiel <- c(1, 2, 3, 4, 100)
mean(beispiel)    # = 22 (stark beeinflusst)
median(beispiel)  # =  3 (robust)
```

**Wann Median verwenden?**

- ✅ Daten haben Ausreißer
- ✅ Verteilung ist schief
- ✅ Ordinalskalierte Daten
- ✅ Einkommen, Preise, Reaktionszeiten

---

#### Modus

```r
# R hat keine eingebaute Modus-Funktion – eigene Funktion erstellen:
get_modus <- function(x) {
  unique_x <- unique(x)
  unique_x[which.max(tabulate(match(x, unique_x)))]
}

# Modus berechnen
get_modus(df$cyl)

# Häufigkeitstabelle anzeigen
table(df$cyl)
```

**Wann Modus verwenden?**

- ✅ Nominale (kategoriale) Daten
- ✅ Diskrete Daten mit wiederholten Werten
- ✅ Wenn man den häufigsten Wert/die häufigste Kategorie wissen möchte

---

### 📏 Streuungsmaße in R

#### Spannweite (Range)

```r
# Minimum und Maximum
range(df$mpg)

# Spannweite
diff(range(df$mpg))

# Oder direkt:
max(df$mpg) - min(df$mpg)
```

> ⚠️ Die Spannweite ist **sehr empfindlich gegenüber Ausreißern**, da sie nur auf den extremsten Werten basiert.

---

#### Varianz

```r
# Varianz berechnen
var(df$mpg)

# Manuelle Berechnung (zum Verstehen)
mittelwert   <- mean(df$mpg)
varianz_manuell <- sum((df$mpg - mittelwert)^2) / (length(df$mpg) - 1)
varianz_manuell
```

> Die Varianz wird in **quadrierten Einheiten** angegeben und ist daher schwer direkt zu interpretieren. Besser: Standardabweichung verwenden.

---

#### Standardabweichung (SD)

```r
# Standardabweichung berechnen
sd(df$mpg)

# Zusammenhang: SD ist Wurzel aus Varianz
sqrt(var(df$mpg))
```

**Standardabweichung interpretieren:**

- Kleine SD → Werte liegen nah am Mittelwert
- Große SD → Werte sind weit gestreut
- SD = 0 → alle Werte sind gleich

**Wann Standardabweichung verwenden?**

- ✅ Daten sind annähernd normalverteilt
- ✅ Immer gemeinsam mit dem Mittelwert berichten
- ✅ Interpretierbar in der Originaleinheit der Daten
- ❌ **Nicht geeignet bei:** Ausreißern oder schiefen Daten → IQR bevorzugen

---

#### Interquartilsabstand (IQR)

```r
# IQR berechnen
IQR(df$mpg)

# Quartile anzeigen
quantile(df$mpg)

# Bestimmte Quartile berechnen
quantile(df$mpg, probs = c(0.25, 0.50, 0.75))
```

**Was der IQR zeigt:** Der IQR beschreibt den Bereich, in dem die **mittleren 50 %** der Daten liegen (zwischen Q1 und Q3).

**Wann IQR verwenden?**

- ✅ Daten haben Ausreißer
- ✅ Daten sind schief verteilt
- ✅ Immer gemeinsam mit dem Median berichten

---

### 📋 Vollständige deskriptive Statistik

```r
# Überblick mit base R
summary(df$mpg)

# Detaillierte Statistiken mit psych-Paket
library(psych)
describe(df$mpg)

# Mehrere Variablen gleichzeitig
describe(df[, c("mpg", "hp", "wt")])
```

**Eigene Zusammenfassungsfunktion:**

```r
zusammenfassung <- function(x, variablenname = "Variable") {
  cat("=== Deskriptive Statistik:", variablenname, "===\n")
  cat("N          :", length(x), "\n")
  cat("Fehlend    :", sum(is.na(x)), "\n")
  cat("Mittelwert :", round(mean(x, na.rm = TRUE), 2), "\n")
  cat("Median     :", round(median(x, na.rm = TRUE), 2), "\n")
  cat("SD         :", round(sd(x, na.rm = TRUE), 2), "\n")
  cat("Varianz    :", round(var(x, na.rm = TRUE), 2), "\n")
  cat("Minimum    :", round(min(x, na.rm = TRUE), 2), "\n")
  cat("Maximum    :", round(max(x, na.rm = TRUE), 2), "\n")
  cat("Spannweite :", round(max(x, na.rm = TRUE) - min(x, na.rm = TRUE), 2), "\n")
  cat("Q1         :", round(quantile(x, 0.25, na.rm = TRUE), 2), "\n")
  cat("Q3         :", round(quantile(x, 0.75, na.rm = TRUE), 2), "\n")
  cat("IQR        :", round(IQR(x, na.rm = TRUE), 2), "\n")
  cat("Schiefe    :", round(moments::skewness(x, na.rm = TRUE), 2), "\n")
  cat("Kurtosis   :", round(moments::kurtosis(x, na.rm = TRUE), 2), "\n")
}

# Anwenden:
zusammenfassung(df$mpg, "Kraftstoffverbrauch (MPG)")
```

---

### 🎯 Entscheidungshilfe: Welches Maß wählen?

| Dateneigenschaft | Lagemaß | Streuungsmaß | Begründung |
|-----------------|---------|--------------|-----------|
| Normalverteilung, keine Ausreißer | Mittelwert | Standardabweichung | Alle Datenpunkte werden genutzt |
| Schiefe Verteilung | Median | IQR | Robust gegenüber Extremwerten |
| Ausreißer vorhanden | Median | IQR | Nicht durch Extremwerte verzerrt |
| Ordinalskalierte Daten | Median | IQR | Für Rangdaten geeignet |
| Nominale/kategoriale Daten | Modus | – | Einziges sinnvolles Maß |
| Für weitere Tests (Regression, ANOVA) | Mittelwert | Varianz/SD | Parametrische Tests erfordern dies |

---

### ✅ Wissenstest: Mittel- und Streuwerte

**Frage 3:** Ein Datensatz enthält Haushaltseinkommen. Mittelwert = 55.000 €, Median = 35.000 €. Was schließen Sie daraus?

```
[( )] Die Daten sind normalverteilt.
[( )] Es gibt keine Ausreißer.
[(X)] Die Daten sind rechtsschiefe – wenige sehr hohe Einkommen verzerren den Mittelwert.
[( )] Median und Mittelwert können nicht so weit auseinanderliegen.
[[?]] **Erklärung:** Wenn der Mittelwert deutlich größer als der Median ist, weisen die Daten eine Rechtsschiefer auf. Einige sehr hohe Einkommen (Ausreißer) ziehen den Mittelwert nach oben. Der Median ist hier das geeignetere Lagemaß.
```

---

**Frage 4:** Datensatz A: Mittelwert = 50, SD = 2. Datensatz B: Mittelwert = 50, SD = 15. Welche Aussage stimmt?

```
[( )] Datensatz A hat mehr Streuung.
[(X)] Datensatz B hat mehr Streuung.
[( )] Beide Datensätze haben dieselbe Streuung.
[( )] Ohne die Rohdaten kann man nichts sagen.
[[?]] **Erklärung:** Eine größere Standardabweichung bedeutet mehr Streuung. Datensatz B (SD = 15) hat deutlich mehr Variabilität als Datensatz A (SD = 2) – die Werte in B liegen weiter vom Mittelwert entfernt.
```

---

**Frage 5:** Wann ist der IQR dem SD vorzuziehen?

```
[( )] Wenn die Daten normalverteilt sind.
[( )] Wenn sehr viele Datenpunkte vorliegen.
[(X)] Wenn Ausreißer vorhanden sind oder die Daten schief verteilt sind.
[( )] Wenn man für weitere statistische Tests plant.
[[?]] **Erklärung:** Der IQR ist ein robustes Maß und wird durch Ausreißer kaum beeinflusst. Er eignet sich besonders bei schiefen Verteilungen oder wenn Extremwerte vorhanden sind. Die SD hingegen ist sensitiv gegenüber Ausreißern.
```

---

**Frage 6:** Welches Maß ist für nominalskalierte Daten (z. B. Lieblingsfarbe) am geeignetsten?

```
[( )] Mittelwert
[( )] Median
[(X)] Modus
[( )] Standardabweichung
[[?]] **Erklärung:** Der Modus (häufigster Wert) ist das einzige sinnvolle Lagemaß für nominale Daten. Mittelwert und Median setzen mindestens ordinale Daten voraus. Die Standardabweichung setzt sogar metrische Daten voraus.
```

---

## 🔗 Teil 3 – Pearson's Korrelationskoeffizient r

### Was ist Korrelation?

{{1}}
Korrelation misst den **linearen Zusammenhang** zwischen zwei metrischen Variablen. Der Pearson-Korrelationskoeffizient r gibt an:

- **Richtung** des Zusammenhangs (positiv oder negativ)
- **Stärke** des Zusammenhangs (von 0 bis ±1)

{{2}}
> **Achtung: Korrelation ≠ Kausalität!**
> Zwei Variablen können stark korrelieren, ohne dass eine die andere verursacht.

---

### Voraussetzungen für Pearson's r

Bevor Sie Pearson's r berechnen, prüfen Sie:

- ✅ Beide Variablen sind **metrisch skaliert** (Intervall oder Verhältnis)
- ✅ Der Zusammenhang ist **linear** (prüfen mit Streudiagramm)
- ✅ Beide Variablen sind **annähernd normalverteilt**
- ✅ Keine starken **Ausreißer**

> Wenn diese Voraussetzungen nicht erfüllt sind, verwenden Sie stattdessen **Spearman's ρ (rho)**.

---

### 📐 Streudiagramm erstellen

**Immer zuerst visualisieren!**

```r
# Einfaches Streudiagramm
plot(df$wt, df$mpg,
     main = "Zusammenhang: Fahrzeuggewicht und Kraftstoffverbrauch",
     xlab = "Gewicht (1000 Pfund)",
     ylab = "Kraftstoffverbrauch (MPG)",
     pch  = 19,
     col  = "steelblue")

# Regressionsgerade hinzufügen
abline(lm(mpg ~ wt, data = df), col = "red", lwd = 2)
```

**Mit ggplot2:**

```r
ggplot(df, aes(x = wt, y = mpg)) +
  geom_point(color = "steelblue", size = 3, alpha = 0.7) +
  geom_smooth(method = "lm", se = TRUE,
              color = "red", fill = "lightcoral", alpha = 0.2) +
  labs(title   = "Zusammenhang: Gewicht und Kraftstoffverbrauch",
       subtitle = "Regressionsgerade mit 95%-Konfidenzband",
       x = "Gewicht (1000 Pfund)",
       y = "Kraftstoffverbrauch (MPG)") +
  theme_minimal()
```

---

### 🔢 Pearson's r berechnen

```r
# Pearson-Korrelation (Standardmethode)
cor(df$wt, df$mpg, method = "pearson")

# Mit Signifikanztest
cor.test(df$wt, df$mpg, method = "pearson")
```

**Ausgabe interpretieren:**

```
Pearsons Produkt-Moment-Korrelation

data:  df$wt and df$mpg
t = -9.559, df = 30, p-value = 1.294e-10
95%-Konfidenzintervall:
 -0.9338264 -0.7440872
Schätzung:
cor
-0.8676594
```

| Ausgabefeld | Bedeutung |
|-------------|-----------|
| `cor` | Pearson's r = −0,87 |
| `t` | t-Teststatistik |
| `df` | Freiheitsgrade |
| `p-value` | Signifikanz (< 0,001 → hoch signifikant) |
| Konfidenzintervall | Bereich, in dem r mit 95 % Wahrscheinlichkeit liegt |

---

### 📊 r interpretieren

| r-Wert | Stärke | Richtung |
|--------|--------|----------|
| +1,00 | Perfekt positiv | Je mehr X, desto mehr Y |
| +0,70 bis +0,99 | Stark positiv | |
| +0,40 bis +0,69 | Mittel positiv | |
| +0,10 bis +0,39 | Schwach positiv | |
| 0,00 | Kein linearer Zusammenhang | |
| −0,10 bis −0,39 | Schwach negativ | |
| −0,40 bis −0,69 | Mittel negativ | |
| −0,70 bis −0,99 | Stark negativ | |
| −1,00 | Perfekt negativ | Je mehr X, desto weniger Y |

> **Faustregeln nach Cohen (1988):**
> r ≈ 0,10 = schwach | r ≈ 0,30 = mittel | r ≈ 0,50 = stark

---

### 📐 Bestimmtheitsmaß r²

```r
# r² berechnen (erklärte Varianz)
r    <- cor(df$wt, df$mpg)
r_sq <- r^2
cat("r =", round(r, 3), "\n")
cat("r² =", round(r_sq, 3), "\n")
cat("Das bedeutet:", round(r_sq * 100, 1),
    "% der Varianz in MPG werden durch das Gewicht erklärt.\n")
```

> **r² = Bestimmtheitsmaß:** Gibt an, wie viel Prozent der Varianz in der einen Variable durch die andere Variable erklärt werden.
>
> Beispiel: r = −0,87 → r² = 0,75 → **75 % der Varianz** in MPG werden durch das Fahrzeuggewicht erklärt.

---

### 🗺️ Korrelationsmatrix für mehrere Variablen

```r
# Korrelationsmatrix für mehrere Variablen
auswahl <- df[, c("mpg", "cyl", "disp", "hp", "wt")]
korr_matrix <- cor(auswahl, method = "pearson")

# Als Tabelle ausgeben (gerundet)
round(korr_matrix, 2)

# Visuell als Korrelogramm (corrplot)
library(corrplot)
corrplot(korr_matrix,
         method  = "color",
         type    = "upper",
         addCoef.col = "black",
         tl.col  = "black",
         tl.srt  = 45,
         title   = "Korrelationsmatrix",
         mar     = c(0, 0, 1, 0))
```

---

### 🔄 Spearman's ρ als Alternative

Wenn die Voraussetzungen für Pearson nicht erfüllt sind:

```r
# Spearman-Rangkorrelation
cor.test(df$wt, df$mpg, method = "spearman")

# Wann Spearman verwenden?
# ✅ Ordinalskalierte Daten
# ✅ Ausreißer vorhanden
# ✅ Kein linearer, aber monotoner Zusammenhang
# ✅ Daten nicht normalverteilt
```

---

### 📝 Ergebnisse berichten

**APA-Stil Beispiel:**

> „Es zeigte sich ein starker negativer Zusammenhang zwischen Fahrzeuggewicht und Kraftstoffverbrauch, r(30) = −.87, p < .001. Schwerere Fahrzeuge verbrauchen tendenziell mehr Kraftstoff (weniger Meilen pro Gallone). Das Gewicht erklärt 75 % der Varianz im Kraftstoffverbrauch (r² = .75)."

**Vorlage für eigene Berichte:**

```
„Es zeigte sich ein [schwacher/mittlerer/starker] [positiver/negativer] Zusammenhang
zwischen [Variable X] und [Variable Y], r([df]) = [Wert], p [< .001 / = Wert].
[Variable X] erklärt [r² × 100] % der Varianz in [Variable Y] (r² = [Wert])."
```

---

### Ergebnisse exportieren

```r
# Korrelationsmatrix als CSV speichern
write.csv(round(korr_matrix, 3),
          "korrelationsmatrix_ihrnachname.csv",
          row.names = TRUE)

# Plot als PNG speichern
png("korrelationsplot_ihrnachname.png", width = 800, height = 600)
corrplot(korr_matrix,
         method  = "color",
         type    = "upper",
         addCoef.col = "black",
         tl.col  = "black",
         title   = "Korrelationsmatrix")
dev.off()
```

---

### ✅ Wissenstest: Pearson's r

**Frage 7:** Sie berechnen r = +0,78 zwischen zwei Variablen. Was bedeutet das?

```
[( )] Es gibt keinen Zusammenhang.
[( )] Es gibt einen schwachen negativen Zusammenhang.
[(X)] Es gibt einen starken positiven Zusammenhang.
[( )] Variable X verursacht Variable Y.
[[?]] **Erklärung:** r = +0,78 liegt im Bereich „stark positiv" (0,70 bis 0,99). Das bedeutet, wenn X steigt, steigt auch Y tendenziell – und zwar stark. Jedoch erlaubt Korrelation keine Aussage über Kausalität!
```

---

**Frage 8:** r = −0,45, p = 0,03. Was schließen Sie?

```
[( )] Es gibt keinen signifikanten Zusammenhang.
[(X)] Es gibt einen signifikanten mittelstarken negativen Zusammenhang.
[( )] Es gibt einen stark negativen Zusammenhang.
[( )] Das Ergebnis ist nicht interpretierbar, da r negativ ist.
[[?]] **Erklärung:** r = −0,45 deutet auf einen mittleren negativen Zusammenhang hin. Da p = 0,03 kleiner als 0,05 ist, ist das Ergebnis statistisch signifikant. Die negative Richtung bedeutet: je höher X, desto niedriger Y.
```

---

**Frage 9:** r = 0,60 zwischen zwei Variablen. Wie viel Prozent der Varianz in Y erklärt X?

```
[( )] 60 %
[(X)] 36 %
[( )] 6 %
[( )] 0,6 %
[[?]] **Erklärung:** Das Bestimmtheitsmaß r² = 0,60² = 0,36 = 36 %. Das bedeutet, dass X 36 % der Varianz in Y erklärt. Die restlichen 64 % werden durch andere Faktoren oder Zufall erklärt.
```

---

**Frage 10:** Wann sollten Sie Spearman's ρ statt Pearson's r verwenden?

```
[( )] Wenn Sie mehr als zwei Variablen haben.
[( )] Wenn die Stichprobe sehr groß ist.
[(X)] Wenn die Daten ordinal skaliert sind oder Ausreißer vorhanden sind.
[( )] Wenn das Ergebnis nicht signifikant ist.
[[?]] **Erklärung:** Spearman's ρ ist die nicht-parametrische Alternative zu Pearson's r. Sie wird verwendet, wenn die Voraussetzungen für Pearson nicht erfüllt sind: ordinale Daten, Ausreißer, nicht-normale Verteilung oder nicht-linearer (aber monotoner) Zusammenhang.
```

---

## ✅ Checkliste: Gute deskriptive Analyse

{{1}} **Vor der Analyse:**

- [ ] Datentyp verstehen (nominal, ordinal, metrisch)
- [ ] Fehlende Werte prüfen (`sum(is.na(df))`)
- [ ] Forschungsfrage klären
- [ ] Datensatz kurz beschreiben (`str(df)`, `head(df)`)

{{2}} **Während der Analyse:**

- [ ] Zuerst visualisieren (Histogramm, Boxplot, Streudiagramm)
- [ ] Ausreißer identifizieren
- [ ] Verteilungsform prüfen (Schiefe, Q-Q-Plot)
- [ ] Geeignete Maße auswählen (Mittelwert+SD oder Median+IQR)
- [ ] Korrelationen nur nach Streudiagramm berechnen
- [ ] Code mit Kommentaren dokumentieren

{{3}} **Für die Berichterstattung:**

- [ ] Stichprobengröße N immer angeben
- [ ] Lagemaß und Streuungsmaß kombiniert berichten (z. B. M = 20,09, SD = 6,03)
- [ ] Einheitliche Dezimalstellen (meist 2)
- [ ] Maßeinheiten angeben
- [ ] Signifikanzwerte bei Korrelationen berichten (r, df, p)
- [ ] Visualisierungen einbinden
- [ ] APA-Format für Statistiken beachten

---

## 📚 Zusammenfassung

### Die wichtigsten Goldregeln

1. **Immer zuerst visualisieren** – Grafiken zeigen, was Zahlen verbergen
2. **Normalverteilung → Mittelwert ± SD** verwenden
3. **Ausreißer oder schiefe Daten → Median ± IQR** verwenden
4. **Kategoriale Daten → Modus und Häufigkeiten**
5. **Pearson's r** nur bei metrischen, annähernd normalverteilten Daten ohne starke Ausreißer
6. **Korrelation ≠ Kausalität** – immer im Blick behalten
7. **r²** angeben, um den Anteil der erklärten Varianz zu kommunizieren

### Häufige Fehler vermeiden

- ❌ Mittelwert bei stark schiefen Daten verwenden
- ❌ Fehlende Werte nicht berücksichtigen (na.rm = TRUE vergessen)
- ❌ Datentyp vor der Analyse nicht prüfen
- ❌ Nur das Lagemaß ohne Streuungsmaß berichten
- ❌ Pearson's r berechnen, ohne vorher ein Streudiagramm zu erstellen
- ❌ Korrelation als Beweis für Kausalität interpretieren

---

## 🔗 Weiterführende Materialien

**Englischsprachige Lernmodule (GitHub):**

- [Deskriptive Statistik](https://liascript.github.io/course/?https://raw.githubusercontent.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/refs/heads/main/descriptive_stats_r_V2.md)
- [Visualisierung](https://liascript.github.io/course/?https://raw.githubusercontent.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/refs/heads/main/visualization_r_V2.md)
- [Normalverteilungscheck](https://liascript.github.io/course/?https://raw.githubusercontent.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/refs/heads/main/normality_check_r_V2.md)
- [Korrelation](https://liascript.github.io/course/?https://raw.githubusercontent.com/OVGU-VET-TechEd/Quantitative_Learning_Nuggtes/refs/heads/main/correlation_r_V2.md)

**R-Dokumentation:**

- `?mean`, `?sd`, `?median`, `?IQR`, `?cor.test`
- psych-Paket: `?describe`
- ggplot2: <https://ggplot2.tidyverse.org/>
- corrplot: <https://cran.r-project.org/package=corrplot>

---

## 💬 Kontakt

Bei Fragen wenden Sie sich an das Lehrteam:

- **Hannes Tegelbeckers:** <hannes.tegelbeckers@ovgu.de>
- **Sprechzeiten:** <https://cloud.ovgu.de/call/ciz2te64>

> **Denken Sie daran:** Gute deskriptive Statistik ist das Fundament jeder quantitativen Forschung – nehmen Sie sich die Zeit, Ihre Daten wirklich zu verstehen!

# OpenCoLi-Spezifikation

#### Version 0.1.2

Die Schlüsselwörter "MUSS/MÜSSEN" (*Englisch: "MUST"*), "ERFORDERLICH" (*Englisch: "REQUIRED"*), "SOLLTE" (*Englisch: "SHOULD"*) und "KANN" (*Englisch: "MAY"*) in diesem Dokument sind so zu interpretieren, wie sie in ihrer englischen Übersetzung in [RFC2119 und RFC8174](https://tools.ietf.org/html/bcp14) spezifiziert sind, und nur dann, wenn sie, wie hier, in Großbuchstaben geschrieben sind.

Dieses Spezifikation ist lizenziert unter der [Apache License, Version 2.0](https://opensource.org/license/apache-2-0/).

## Einführung

Das Open Code List Representation Format (OpenCoLi) definiert ein generisches Standard-Datenformat zur Repräsentation von Code-Listen bzw. Schlüsselverzeichnissen. Basierend auf dem [JSON-Standard](https://datatracker.ietf.org/doc/html/rfc8259) kann dieses Format mit nahezu jeder Programmiersprache leicht erzeugt und gelesen werden. Mit Hilfe des [OpenCoLi Document Schema](https://github.com/openpotato/opencoli/tree/main/schemas/v0.1/schema.json) können Dokumente im OpenCoLi-Format auf ihre syntaktische Korrektheit hin validiert werden.

OpenCoLi kann zum Austausch von Code-Listen zwischen Diensten oder Anwendungen genutzt werden, als Repräsentationsformat für ofizielle Code-Listen oder als Antwortformat für API-Anfragen (z.B. für RESTful Web-Services).

### Was sind Code-Listen?

Code-Listen bzw. Schlüsselverzeichnisse sind strukturierte Sammlungen von Codes bzw. Schlüsseln, die zur Identifikation und Klassifikation von Daten verwendet werden. Diese Verzeichnisse sind essenziell in zahlreichen Bereichen wie Datenbanken, Verwaltungssystemen, wissenschaftlichen Forschungen und industriellen Anwendungen. Sie dienen dazu, Daten konsistent und effizient zu organisieren, zu speichern und abzurufen.

Code-Listen spielen eine zentrale Rolle bei der Standardisierung und Harmonisierung von Daten. Durch die Verwendung standardisierter Codes können unterschiedliche Systeme und Organisationen Daten einheitlich interpretieren und austauschen. 

Beispiele für Code-Listen sind:

+ Internationale Codes für Staaten, Sprachen und Währungen der International Organization for Standardization (ISO).

+ Nationale Gebietsschlüssel (z.B. Gemeindeschlüssel der Bundesrepublik Deutschland)

+ Nationale und subnationale Schlüssel im öffentlichen Bereich (z.B. Statistikschlüssel der Statistikbehörden)

Im Prinzip lässt sich jede Datenauswahl auf eine Code-Liste abbilden, selbst ein banaler boolescher Wert kann durch die Codes *Nein* und *Ja* repräsentiert werden. 

### Wie sind Code-Listen aufgebaut?

Die einfachste Form einer Code-Liste besteht aus zwei Spalten: Schlüssel und Beschreibung.

Hier ein Beispiel für ein Länderverzeichnis:

Schlüssel | Beschreibung
--------- | ------------
AT        | Österreich
CH        | Schweiz
DE        | Deutschland

Code-Listen können potentiell bliebig viele Spalten enthalten. Hier ein Beispiel für ein Länderverzeichnis mit drei Spalten:

Schlüssel | Kurzname        | Langname
--------- | --------------- | --------
AT        | Österreich      | Republik Österreich
CH        | Schweiz         | Schweizerische Eidgenossenschaft
DE        | Deutschland     | Bundesrepublik Deutschland

Code-Listen können aufeinander verweisen. Hier ein Beispiel für ein Länderverzeichnis mit Verweis auf ein anderes Kontinenteverzeichnis:

Schlüssel | Kurzname        | Langname                         | Kontinent
--------- | --------------- | -------------------------------- | ---------
AT        | Österreich      | Republik Österreich              | EU
CH        | Schweiz         | Schweizerische Eidgenossenschaft | EU
DE        | Deutschland     | Bundesrepublik Deutschland       | EU 
MA        | Marokko         | Königreich Marokko               | AF

Das passende Kontinenteverzeichnis könnte dann so aussehen:

Schlüssel | Name
--------- | ----
AF        | Afrika
AM        | Amerikas
EU        | Europa
OC        | Ozeanien

Auch können Code-Listen mehr als einen Schlüssel besitzen. Hier ein Beispiel für ein Länderverzeichnis mit verschiedenen ISO3166-Codes:

Alpha2Code | Alpha3Code | NumericCode | Name
---------- | ---------- | ----------- | ----
AT         | AUT        | 040         | Österreich
CH         | CHE        | 756         | Schweiz
DE         | DEU        | 276         | Deutschland

Gehen wir noch einen Schritt weiter mit diesem mehrsprachigen Länderverzeichnis:

Schlüssel | Sprache | Name
--------- | ------- | ----
AT        | de      | Österreich
AT        | en      | Austria
CH        | de      | Schweiz
CH        | en      | Switzerland
DE        | de      | Deutschland
DE        | en      | Germany

Hier ist es die Kombination aus Schlüssel und Sprache (ebenfalls durch einen Sprachschlüssel repräsentiert), welche Eindeutigkeit definiert. 

Der Aufbau von Code-Listen kann also einfach bis einigermaßen komplex sein. 

### Gibt es nicht schon Standards für Code-Listen?

Ja, gibt es: Die [Organization for the Advancement of Structured Information Standards (OASIS)](https://www.oasis-open.org) hat mit [Code List Representation (genericode)](https://docs.oasis-open.org/codelist/genericode/v1.0/genericode-v1.0.html) einen XML-basierten Standard für Code-Listen definiert.

> The OASIS Code List Representation format, “genericode”, is a single model and XML format (with a W3C XML Schema) that can encode a broad range of code list information. The XML format is designed to support interchange or distribution of machine-readable code list information between systems.  Note that genericode is not designed as a run-time format for accessing code list information, and is not optimized for such usage.  Rather, it is designed as an interchange format that can be transformed into formats suitable for run-time usage, or loaded into systems that perform run-time processing using code list information.

Klingt gut, hat aber einen Haken. Es existiert keine JSON-Repräsentation von "genericode". 

> Recognizing the custom use of JSON in a tight binding between user-defined processes, the committee sees no purpose served by standardizing a JSON syntax for the genericode vocabulary.

Das bedeutet, es gibt keine offizielle Unterstützung für JSON als Datenformat und somit auch kein offizielles JSON-Schema.

Wer also Code-Listen im XML-Format repräsentieren möchte, dem sei der OASIS-Standard ans Herz gelegt. Wer aber mit JSON arbeiten möchte, der hat mit OpenCoLi eine passende Alternative parat. Natürlich ist OpenCoLi erheblich vom *OASIS Code List Representation Format* beeinflusst und versucht auch weitestgehend kompatibel zu sein.

## Definitionen

### OpenCoLi-Dokument

Ein OpenCoLi-Dokument ist eine in sich geschlossene Ressource, die entweder eine Code-Liste oder eine Code-Listensammlung definiert und beschreibt. Es MÜSSEN mindestens die Felder `opencoli` sowie im gegenseitigen Ausschluss `codeList` oder `codeListSet` enthalten sein. Ein OpenCoLi-Dokument verwendet die OpenCoLi-Spezifikation und ist mit ihr konform.

### Code-Liste

Eine Code-Liste ist eine klassische relationale Tabelle mit Spalten und Datenzeilen, wobei mindestens eine Spalte als Schlüssel (Code) dienen sollte. OpenCoLi erlaubt es, generische Code-Listen zu definieren.

### Code-Listensammlung

Eine Code-Listensammlung ist eine Liste von Verweisen auf externe Code-Listen. Mit einer Code-Listensammlung können vor allem unterschiedliche Versionen einer Code-Liste zusammengefasst werden.

## Spezifikation

### Versionierung

Die OpenCoLi-Spezifikation wird nach dem Schema `major.minor.patch` versioniert. Der Major-Minor-Teil der Versionsnummer (z. B. `0.1`) MUSS den Funktionssatz der Spezifikation bezeichnen. Die Patch-Versionen betreffen Fehler in diesem Dokument oder stellen Klarstellungen zu diesem Dokument bereit, nicht zum Funktionsumfang. Werkzeuge, die OpenCoLi in der Version `0.1` unterstützen, MÜSSEN mit allen `0.1.*` Versionen von OpenCoLi kompatibel sein. Die Patch-Version SOLLTE von den Werkzeugen NICHT berücksichtigt werden, so dass zum Beispiel kein Unterschied zwischen `0.1.0` und `0.1.1` gemacht wird.

Ein OpenCoLi-Dokument enthält stets ein obligatorisches Feld `opencoli`, das die verwendete Version der OpenCoLi-Spezifikation angibt.

### Format

Ein OpenCoLi-Dokument, das mit der OpenCoLi-Spezifikation konform ist, ist selbst ein JSON-Objekt, das im JSON-Format dargestellt werden kann.

Bei allen Feldnamen in der Spezifikation wird zwischen Groß- und Kleinschreibung unterschieden. Das Schema sieht zwei Arten von Feldern vor: Fest definierte Felder, die einen deklarierten Namen haben, und freie Felder, deren Namen aber einem bestimmten Muster (*Englisch: pattern*) entsprechen MÜSSEN. Zusatzfelder MÜSSEN innerhalb des enthaltenen JSON-Objekts eindeutige Namen haben.

#### JSON Schema

[JSON Schema](https://json-schema.org/) ist eine Spezifikation zur Definition von JSON-Datenstrukturen. Ein JSON-Schema wird selbst deklarativ durch [JSON](https://www.json.org/) ausgedrückt. Das [OpenCoLi Document Schema](https://github.com/openpotato/opencoli/tree/main/schemas/v0.1/schema.json) ist ein JSON-Schema für OpenCoLi-Dokumente.

#### Mehrsprachigkeit

OpenCoLi unterstützt Mehrsprachigkeit, d.h. Text-Strings können optional mit einem oder mehreren [IETF BCP 47-Sprachtags](https://www.rfc-editor.org/rfc/bcp/bcp47.txt) versehen werden. Hinter jedem Sprachtag kann dann die passende Übersetzung angegeben werden.  

Beispiel:

``` json
"description": [
  {
    "language": "en",
    "text": "Currency codes of the International Standard ISO 4217"
  },
  {
    "language": "de",
    "text": "Währungscodes der internationalen Norm ISO 4217"
  }
]
```

#### Sprachtags

IETF BCP 47 (Best Current Practice 47) ist ein Dokument, das die Regeln und Verfahren zur Erstellung und Nutzung von Sprachkennzeichnungen definiert. Diese Kennzeichnungen werden verwendet, um die Sprache von Inhalten im Internet zu identifizieren. BCP 47 wird von der Internet Engineering Task Force (IETF) verwaltet und besteht aus zwei RFCs (Request for Comments): [RFC 5646](https://datatracker.ietf.org/doc/html/rfc5646) und [RFC 4647](https://datatracker.ietf.org/doc/html/rfc4647).

Sprachtags sind notwendig, um die richtige Lokalisierung und Internationalisierung von Webinhalten und Software zu gewährleisten. Sie ermöglichen es Anwendungen und Diensten, den richtigen Sprachinhalt für Benutzer bereitzustellen und die korrekte Darstellung von sprachspezifischen Daten wie Datumsformaten, Zahlen und Textrichtung zu unterstützen.

Ein BCP 47-Sprachtag besteht aus einer Reihe von Untertags, die durch Bindestriche getrennt sind. Diese Untertags definieren verschiedene Aspekte der Sprache und ihrer Varianten. Die grundlegendsten Komponenten sind:

+ **Primärsprachen-Subtag**: Dies ist ein obligatorischer Subtag und besteht aus einem Zwei- oder Drei-Buchstaben-Code, der die Hauptsprache bezeichnet (z.B. `en` für Englisch, `de` für Deutsch).

+ **Region-Subtag**: Ein optionaler Subtag, der ein Land oder eine Region spezifiziert (z.B. `US` für die Vereinigten Staaten in `en-US`).

+ **Skript-Subtag**: Ein optionaler Subtag, der das Schriftsystem angibt (z.B. `Latn` für das lateinische Alphabet in `zh-Latn`).

+ **Variante-Subtag**: Ein optionaler Subtag, der Sprachvarianten oder Dialekte beschreibt (z.B. `1901` für die traditionelle deutsche Rechtschreibung in `de-1901`).

+ **Extension-Subtag**: Erweiterungen zur Angabe zusätzlicher Informationen.

+ **Privatgebrauch-Subtag**: Ein Bereich, der für die private Nutzung reserviert und nicht standardisiert ist.

#### Datums- und Zeitangaben

Die Formatierung der Datums- und Zeitangaben in OpenCoLi sind, wie von [JSON Schema](https://json-schema.org/understanding-json-schema/reference/string.html#dates-and-times) vorgegeben, durch [RFC 3339, Abschnitt 5.6](https://tools.ietf.org/html/rfc3339#section-5.6) spezifiziert.

Beispiele:

+ **`date-time`** : Datum und Zeit zusammen, z.B. `2024-11-13T20:20:39` oder `2024-11-13T20:20:39+00:00`.
+ **`time`** : Nur Uhrzeit, z.B. `20:20:39` oder `20:20:39+00:00`.
+ **`date`** : Nur Datum, z.B. `2024-11-13`.

#### URIs

Wird ein [Uniform Resource Identifier (URI)](https://tools.ietf.org/html/rfc3986) verlangt, muss je nach Kontext zwischen zwei JSON-String-Formaten unterscheiden werden:

+ Das Format `uri` erwartet, dass der JSON-String ein absoluter URI (Uniform Resource Identifier) ist. Ein absoluter URI enthält alle notwendigen Informationen, um die Ressource unabhängig von ihrem Kontext zu identifizieren. Das bedeutet, dass ein `uri`

    + mit einem Schemas (wie http, https, etc.) beginnt,
    + einen Hostnamen oder eine IP-Adresse enthalten kann,
    + und optional einen Pfad, eine Abfrage und ein Fragment enthalten kann.

+ Das Format `uri-reference` ist flexibler und erlaubt sowohl absolute als auch relative URIs. `uri-reference` kann also eine vollständige URI wie oben beschrieben sein, oder es kann ein relativer Verweis sein, der in einem bestimmten Kontext interpretiert werden muss. Ein relativer URI könnte beispielsweise nur einen Pfad oder sogar nur ein Fragment enthalten.

Beispiele:

+ **`uri`** : 
    + `https://example.com/path/to/resource?query=param#fragment`
+ **`uri-reference`** : 
    + `https://example.com/path/to/resource?query=param#fragment`
	+ `/path/to/resource`
	+ `#fragmen`

### Schema

#### OpenCoLi-Objekt

Dies ist das Wurzelobjekt eines OpenCoLi-Dokuments und enthält folgende Felder:

**`opencoli`**

:   Ein JSON-String mit der Versionsnummer der OpenCoLi-Spezifikation. **Dieses Feld ist ERFORDERLICH**.

**`codeList`**

:   Ein `codeList`-Objekt, das die Spaltendefinitionen und den Dateninhalt einer Code-Liste beinhaltet. 

**`codeListSet`**

:   Ein `codeListSet`-Objekt, das eine Sammlung von Code-Listen definiert. 

Die Felder `codeList` und `codeListSet` schließen sich gegensietig aus. **Eines von beiden Feldern ist ERFORDERLICH**

#### codeList-Objekt

Das `codeList`-Objekt definiert eine komplette Code-Liste samt Daten:

**`info`**

:   Ein `codeListInfo`-Objekt mit Metadaten zur Code-Liste. **Dieses Feld ist ERFORDERLICH**.

**`columnSet`**

:   Ein `columnSet`-Objekt, das die Spalten und eindeutigen Schlüssel der Code-Liste definiert. **Dieses Feld ist ERFORDERLICH**.

**`dataSet`**

:   Ein `dataSet`-Objekt, das die Datenzeilen der Code-Liste enthält.

#### codeListInfo-Objekt

Das `codeListInfo`-Objekt enthält Metadaten zur Code-Liste:

**`shortName`**

:   Ein JSON-String mit dem Kurznamen der Code-Liste.

**`longName`**

:   Der Langname der Code-Liste. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`description`**

:   Eine ausführliche Beschreibung der Code-Liste. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`notes`**

:   Zusätzliche Bemerkungen zur Code-Liste. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`changeLog`**

:   Erlaubt es, die Änderungen gegenüber vorherigen Versionen dieser Code-Liste zu dokumentieren. Es MUSS ein JSON-Array mit JSON-Strings sein.

**`agency`**

:   Ein `agency`-Objekt mit Informationen über die Stelle, die für die Veröffentlichung und/oder Pflege der Codes zuständig ist.

**`version`**

:   Ein JSON-String mit der Version der Code-Liste.

**`validFrom`**

:   Ein JSON-String im Format `date-time`, der den Zeitpunkt definiert, ab dem diese Code-Liste gültig ist.

**`validTo`**

:   Ein JSON-String im Format `date-time`, der den Zeitpunkt definiert, bis zu dem diese Code-Liste noch gültig ist.

**`canonicalUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert alle Versionen (zusammen) dieser Code-Liste eindeutig. 

**`canonicalVersionUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert eine bestimmte Version dieser Code-Liste. **Dieses Feld ist ERFORDERLICH**.

**`locationUri`**

:   Entweder ein JSON-String im Format `uri` oder ein JSON-Array mit JSON-String-Werten im Format `uri`. Diese URIs sind vorgeschlagene Abruforte für die referenzierte Code-Liste, im OpenCoLi-Format.

**`publishedAt`**

:   Ein JSON-String im Format `date-time` mit dem Zeitpunkt der Veröffentlichung dieser Code-Liste.

**`publishedFrom`**

:   Ein `publisher`-Objekt mit Informationen über den Herausgeber dieser Code-Liste.

**`language`**

:   Ein JSON-String mit der Sprache dieser Code-Liste. Dies MUSS ein [IETF BCP 47-Sprachtag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt) sein.

Dieses Objekt KANN erweitert werden.

Beispiel:

``` json
"info": {
  "shortName": "GermanFederalStateCodes",
  "longName": "ISO 3166-2 Codes for Germany",
  "description": "ISO 3166-2 Codes for the federal states of Germany",
  "agency": {
	"shortName": "ISO",
	"longName": "International Organization for Standardization",
	"url": "https://www.iso.org/"
  },
  "version": "2024-07-12",
  "canonicalUri": "urn:iso:std:iso:3166-2",
  "canonicalVersionUri": "urn:iso:std:iso:3166-2:2024-07-12",
  "locationUri": "https://github.com/openpotato/opencoli/tree/main/samples/germany.federal-state-codes-2024-07-12.json",
  "publishedAt": "2024-07-12T10:00:00",
  "publishedFrom": {
	"shortName": "OpenCoLi",
	"url": "https://openpotato.github.io/opencoli/"
  },
  "language": "en"
}
```

#### codeListSet-Objekt

Das `codeListSet`-Objekt definiert eine Sammlung von Code-Listen:

**`info`**

:   Ein `codeListSetInfo`-Objekt mit Metadaten zur Code-Listensammlung. **Dieses Feld ist ERFORDERLICH**.

**`codeLists`**

:   Eine Liste von Code-Listen. Es MUSS ein JSON-Array mit `codeListRef`-Objekten sein. **Dieses Feld ist ERFORDERLICH**.

#### codeListSetInfo-Objekt

Das `codeListSetInfo`-Objekt enthält Metadaten zu einer Code-Listensammlung:

**`shortName`**

:   Ein JSON-String mit dem Kurznamen der Code-Listensammlung.

**`longName`**

:   Der Langname der Code-Listensammlung. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`description`**

:   Eine kurze Beschreibung der Code-Listensammlung. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`summary`**

:   Eine längere Zusammenfassung zum Inhalt der Code-Listensammlung. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`language`**

:   Ein JSON-String mit der Sprache dieser Code-Listensammlung. Dies MUSS ein [IETF BCP 47-Sprachtag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt) sein.

Dieses Objekt KANN erweitert werden.

Beispiel:

``` json
"info": {
  "shortName": "GermanFederalStateCodeListSet",
  "longName": "ISO 3166-2 Code Lists for Germany",
  "description": "All versions of the ISO 3166-2 Code Lists for the federal states of Germany",
  "language": "en"
}
```

#### codeListRef-Objekt

Das `codeListRef`-Objekt definiert einen Verweis auf eine externe Code-Liste:

**`canonicalUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert alle Versionen (zusammen) der referenzierten Code-Liste eindeutig. 

**`canonicalVersionUri`**

:   Ein JSON-String im Format `uri`. Diese URI identifiziert eine bestimmte Version der referenzierten Code-Liste. **Dieses Feld ist ERFORDERLICH**.

**`locationUri`**

:   Entweder ein JSON-String im Format `uri` oder ein JSON-Array mit JSON-String-Werten im Format `uri`. Diese URIs sind vorgeschlagene Abruforte für die referenzierte Code-Liste, im OpenCoLi-Format.

#### columnSet-Objekt

Das `columnSet`-Objekt definiert Spalten und eindeutigen Schlüssel einer Code-Liste:

**`columns`**

:   Definiert die Spalten der Code-Liste. Es MUSS ein JSON-Array mit `column`-Objekten sein. **Dieses Feld ist ERFORDERLICH**.

**`keys`**

:   Definiert die eindeutigen Schlüssel der Code-Liste. Es MUSS ein JSON-Array mit `key`-Objekten sein.

**`defaultKey`**

:   Ein JSON-String mit einer ID, die auf ein `key`-Objekt verweist. Damit kann bei mehreren Schlüsseln der Standardschlüssel definiert werden, also jenen Schlüssel aus `keys`, der bevorzugt als Code-Quelle genutzt werden soll.

**`references`**

:   Definiert interne Referenzen sowie externe Referenzen zu anderen Code-Listen. Es MUSS ein JSON-Array mit `reference`-Objekten sein.

#### column-Objekt

Das `column`-Objekt definiert eine Spalte für einer Code-Liste:

**`id`**

:   Ein JSON-String mit der ID der Spalte. **Dieses Feld ist ERFORDERLICH**.

**`name`**

:   Der Name der Spalte. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`description`**

:   Eine kurze Beschreibung der Spalte. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`type`**

:   Definiert den Datentyp der Spalte. **Dieses Feld ist ERFORDERLICH**. Es MUSS ein JSON-String mit einem dieser Werte sein:
    + `string`
    + `enum`
    + `enum-set`
    + `integer`
    + `number`
    + `bool`
    + `time`
    + `date`
    + `date-time`
    + `object`

**`nullable`**

:   Eine JSON-Boolean, der definiert, on diese Spalte auch JSON-NULLs enthalten darf.

Abhängig vom Wert in `type` sind weitere Eigenschaften verfügbar.

##### string

Repräsentiert einen JSON-String. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ `minLength` : Eine JSON-Nummer im Format `integer` mit der minimalen zulässigen Anzahl an Zeichen
+ `maxLength` : Eine JSON-Nummer im Format `integer` mit der maximalen zulässigen Anzahl an Zeichen
+ `pattern` : Eine JSON-String mit einem regulären Ausdruck, der stets zu den Werten in dieser Spalte passen muss.
+ `language` : Ein JSON-String mit der Sprache für die Inhalte dieser Spalte. Dies MUSS ein [IETF BCP 47-Sprachtag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt) sein.

##### enum

Einen JSON-String, der ein Aufzählung repräsentiert. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ `members` : Definiert die möglichen Werte der Auszählung. Es MUSS ein JSON-Array mit `enumMember`-Objekten sein. **Dieses Feld ist ERFORDERLICH**.

##### enum-set

Einen JSON-String, der eine Aufzählungmenge repräsentiert, formatiert als CSV-String. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ `delimiterChar` : Ein JSON-String, der das CSV-Trennzeichen definiert.
+ `quoteChar` : Ein JSON-String, der das CSV-Begrenzungszeichen definiert.
+ `members` : Definiert die möglichen Werte der Aufzählung. Es MUSS ein JSON-Array mit `enumMember`-Objekten sein. **Dieses Feld ist ERFORDERLICH**.

##### integer

Repräsentiert eine JSON-Nummer im Format `integer`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ `minValue` : Eine JSON-Nummer im Format `integer` mit dem zulässigen Minimalwert
+ `maxValue` : Eine JSON-Nummer im Format `integer` mit dem zulässigen Minimalwert

##### number

Repräsentiert eine JSON-Nummer im Format `number`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ `minValue` : Eine JSON-Nummer im Format `number` mit dem zulässigen Minimalwert
+ `maxValue` : Eine JSON-Nummer im Format `number` mit dem zulässigen Minimalwert

##### date

Repräsentiert ein JSON-String im Format `date`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ `minValue` : Ein JSON-String im Format `date` mit dem zulässigen Minimalwert
+ `maxValue` : Ein JSON-String im Format `date` mit dem zulässigen Minimalwert

##### date-time

Repräsentiert ein JSON-String im Format `date-time`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ `minValue` : Ein JSON-String im Format `date-time` mit dem zulässigen Minimalwert
+ `maxValue` : Ein JSON-String im Format `date-time` mit dem zulässigen Minimalwert

##### time

Repräsentiert ein JSON-String im Format `time`. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ `minValue` : Ein JSON-String im Format `time` mit dem zulässigen Minimalwert
+ `maxValue` : Ein JSON-String im Format `time` mit dem zulässigen Minimalwert

##### object

Repräsentiert ein JSON-Objekt. Die folgenden zusätzlichen Schema-Eigenschaften sind verfügbar:

+ `schemaUri` : Ein JSON-String im Format `uri`. Diese URI ist der Abrufort des JSON-Schemas für diese Spalte.

#### enumMember-Objekt

Das `enumMember`-Objekt definiert einen Wert in einer Aufzählung:

**`value`**

:   Ein JSON-String mit dem Aufzählungswert. **Dieses Feld ist ERFORDERLICH**.

**`description`**

:   Die Beschreibung des Aufzählungswertes. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

#### key-Objekt

Das `key`-Objekt definiert einen eindeutigen Schlüssel für die Code-Liste:

**`id`**

:   Ein JSON-String mit der eindeutigen ID des Schlüssels. **Dieses Feld ist ERFORDERLICH**.

**`name`**

:   Der Name des Schlüssels. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`description`**

:   Eine kurze Beschreibung des Schlüssels. Dies ist entweder ein normaler JSON-String oder ein JSON-Array mit `localizableString`-Objekten.

**`columnRefs`**

:   Ein JSON-String-Array mit IDs, die jeweils auf ein `column`-Objekt verweisen. **Dieses Feld ist ERFORDERLICH**.

#### reference-Objekt

Das `reference`-Objekt definiert eine Referenz zur eigenen oder zu einer externen Code-Liste:

**`codeListRef`**

:   Ein `codeListRef`-Objekt, das auf eine externe Code-Liste verweist. Ist `codeListRef` definiert, verweist `keyRef` auf ein `key`-Objekt aus der referenzierten Code-Liste, andernfalls auf ein `key`-Objekt dieser Code-Liste.

**`keyRef`**

:   Ein JSON-String mit einer ID, die auf ein `key`-Objekt verweist. **Dieses Feld ist ERFORDERLICH**.

#### dataSet-Objekt

Das `dataSet`-Objekt enthält die Daten einer Code-Liste:

**`rows`**

:   Definiert die Datenzeilen der Code-Liste. Es MUSS ein JSON-Array mit `row`-Objekten sein. **Dieses Feld ist ERFORDERLICH**.

#### row-Objekt

Das `row`-Objekt definiert eine Datenzeile in einer Code-Liste:

**`cells`**

:   Definiert die Datenzellen der Datenzeile. Es MUSS ein JSON-Array mit `cell`-Objekten sein. **Dieses Feld ist ERFORDERLICH**.

#### cell-Objekt

Das `cell`-Objekt definiert den Inhalt einer Datenzelle:

**`columnRef`**

:   Ein Verweis auf eine Code-Listenspalte. Ein JSON-String mit einer ID, die auf ein `column`-Objekt verweist.

**`value`**

:   Definiert den eigentlichen Wert der Datenzelle. Je nach Spaltendefinition kann dies ein JSON-Null-Wert, ein JSON-String, ein JSON-Numeric, ein JSON-Objekt oder ein JSON-Array mit `localizableString`-Objekten sein. **Dieses Feld ist ERFORDERLICH**.

#### localizableString-Objekt

Das `localizableString`-Objekt ist Text-String, der mit einem Sprach-Code verbunden ist:

**`language`**

:   Ein JSON-String mit einem Sprachtag. Dies MUSS ein [IETF BCP 47-Sprachtag](https://www.rfc-editor.org/rfc/bcp/bcp47.txt) sein. **Dieses Feld ist ERFORDERLICH**.

**`text`**

:   Der eigentliche Text. **Dieses Feld ist ERFORDERLICH**.

Beispiel:

``` json
"longName": [
  {
    "language": "de",
    "text": "ISO 3166-2 Codes für Deutschland"
  },
  {
    "language": "en",
    "text": "ISO 3166-2 Codes for Germany"
  }
]
```

### Erweiterung der Spezifikation

Die OpenCoLi-Spezifikation kann an bestimmten Stellen um zusätzliche Daten erweitert werden.

Die Eigenschaften der Erweiterungen sind als freie Felder implementiert, denen immer ein `x-` vorangestellt werden MUSS (z.B. `x-external-id`). Der Wert kann eine Zeichenfolge, eine Zahl, ein boolescher Wert, Null, ein Objekt oder ein Array sein.

Die Erweiterungen können von den verfügbaren Werkzeugen unterstützt werden oder auch nicht. Idealerweise können diese Werkzeuge erweitert werden, um die gewünschte Unterstützung hinzuzufügen (z.B. bei Open Source-Projekten).

Beispiel:

``` json
"info": {
  "shortName": "GermanFederalStateCodes",
  "agency": {
	"shortName": "ISO",
	"longName": "International Organization for Standardization",
    "x-contact-name": "ISO Central Secretariat",
    "x-contact-address": "Chemin de Blandonnet 8, 1214 Geneva, Switzerland",
    "x-contact-email": "central@iso.org "
  }
}
```
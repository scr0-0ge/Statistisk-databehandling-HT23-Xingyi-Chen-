Formler och satser för grundläggande sannolikhet
Låt oss bryta ner dessa koncept genom att använda exemplet från Uppgift 3 för att illustrera dem:

### 1. Villkorlig Sannolikhet
**Koncept**: Det är som att uppdatera vad du tror är oddsen för att något ska hända när du får ny information. Till exempel, om du vet att en kund är en tjuv, hur ändrar det oddsen att de bär en mössa?

**I Praktiken**:
- **P(M|T)**: Sannolikheten att en kund bär en mössa (M) givet att de är en tjuv (T). Här är det 90% eller 0,9.
- **P(M|T^c)**: Sannolikheten att en kund bär en mössa givet att de inte är en tjuv (T^c). Detta är 10% eller 0,1.

### 2. Satsen om Total Sannolikhet
**Koncept**: När du vill ta reda på chanserna för att något ska hända, men detta något kan hända på olika sätt, så adderar du chanserna för varje sätt det kan hända.

**I Praktiken**:
- Att finna P(M), sannolikheten att en slumpmässig kund bär en mössa:
  \[ P(M) = P(M|T)P(T) + P(M|T^c)P(T^c) \]
- Här är P(T) = 1% eller 0,01 (sannolikheten att vara en tjuv) och P(T^c) = 99% eller 0,99 (sannolikheten att inte vara en tjuv).

### 3. Bayes Teorem
**Koncept**: Detta är som omvänd konstruktion i sannolikhet. Du börjar med utfallet och arbetar baklänges för att hitta orsaken. Till exempel, om du vet att en kund bär en mössa, hjälper Bayes teorem dig att räkna ut hur sannolikt det är att de är en tjuv.

**I Praktiken**:
- Beräkna P(T|M), sannolikheten att en kund är en tjuv givet att de bär en mössa:
  \[ P(T|M) = \frac{P(M|T)P(T)}{P(M)} \]

### 4. Komplementära Sannolikheter
**Koncept**: Detta är idén att chansen att något inte händer är bara motsatsen till chansen att det händer. Om du känner till oddsen för att det ska regna, så är oddsen för att det inte ska regna bara det som är kvar.

**I Praktiken**:
- P(T^c) = 1 - P(T). Om det finns 1% chans för att en kund är en tjuv, finns det 99% chans att de inte är det.

### 5. Grundläggande Sannolikhetsberäkningar
**Koncept**: Detta är dina raka matematiska beräkningar, som att addera, subtrahera och multiplicera sannolikheter, för att räkna ut dina svar.

**I Praktiken**:
- Tillämpning av dessa beräkningar i Satsen om Total Sannolikhet och Bayes Teorem.

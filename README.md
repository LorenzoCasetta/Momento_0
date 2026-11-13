Momento_N — Buona visione e buona scelta — Off Rocker

## Cosa fa

`Momento_0.html` è un file unico, senza dipendenze e senza rete. Si apre in un browser con WebGL2.

1. **Scrive uno shader.** A ogni nuova superficie scrive un fragment shader GLSL ES 3.0 da circa 270 numeri casuali. Lo shader usa una libreria fissa: rumore a gradiente, frattale (nuvola, cresta, bolla) e celle di Voronoi.
2. **Compone la superficie.** Fondo più 0–5 strati.
   - **Campi:** 16 tipi, ciascuno con 0–3 deformazioni del dominio (6 tipi) e una curva del valore (7 tipi).
   - **Strati:** ognuno è una maschera presa da un campo. Ha:
     - bordo netto o sfumato, a spruzzo o a nastro;
     - intersezione o sottrazione opzionale con la maschera precedente;
     - colore da una rampa di 3–6 tinte (12 strategie di tavolozza);
     - fusione normale, moltiplica, scherma o sovrapponi;
     - orlo opzionale, altezza e lucido.
3. **Scarta le superfici deboli.** Prima di mostrarla, la disegna a 64×40 px e la scarta se il contrasto tra blocchi di 8×8 px è sotto 0,04 o se oltre il 45% dei pixel è quasi nero o quasi bianco. Fa fino a 8 tentativi.
4. **Disegna in due buffer:** colore (RGBA8) e altezza (RGBA16F), entrambi con mipmap.
5. **Passaggio ottico fisso, 32 parametri per superficie:**
   - normali dall'altezza su due scale, luce direzionale con tinta calda/fredda, speculare dal lucido;
   - scurimento delle cavità, schiarimento delle creste;
   - sfasamento RGB, sfocatura graduale, alone;
   - retino, posterizzazione con dithering Bayer o casuale;
   - grana, contrasto, saturazione, gamma.
6. **Nel tempo**, a ogni intervallo di *Generazione*:
   - **Evoluzione:** nell'80% dei casi sposta una parte dei valori continui della superficie corrente, che li raggiunge gradualmente senza ricompilare lo shader. Nel 15% genera una discendente: tiene il primo 30–95% dei numeri e rigenera il resto. Nel 5% genera una superficie nuova.
   - **Nascita:** aggiunge una superficie nuova finché ce ne sono meno di 3.
   - **Transizione:** una superficie nuova compare in *Durata* secondi seguendo una soglia su un suo campo, oppure in dissolvenza. Quando è piena, quelle sotto vengono eliminate.
7. **Esporta PNG** alla risoluzione dello schermo, a 4096 px o a 8192 px sul lato lungo, ridisegnando a quella risoluzione. 8192 compare solo se la GPU lo supporta e il dispositivo non è touch o con poca memoria.
8. **Legge i colori a schermo.** Restituisce fino a 6 colori dominanti in esadecimale.
9. **Adatta la risoluzione.** Lato lungo massimo 1920 px. La riduce a passi del 15% se i frame superano 50 ms e riprova più in alto a ogni nuova superficie.

## Comandi

| Tasto / controllo | Azione |
|---|---|
| Clic, Spazio, Invio | mostra / nasconde il pannello |
| C | cattura |
| P | ferma / riprende |
| → | superficie nuova |
| E / N | Evoluzione / Nascita |
| F | schermo intero |
| Generazione | 1–30 s tra un evento e il successivo |
| Durata | 1–40 s di transizione |
| Cartella | salva le catture in una cartella scelta (solo browser Chromium) |

Le impostazioni restano nel `localStorage` del browser.

## Licenza

Codice: MIT (`LICENSE`). Font incorporato Redaction 50 © 2019 MCKL: SIL Open Font License 1.1 (`OFL.txt`).

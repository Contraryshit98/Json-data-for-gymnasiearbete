# Json-data-for-gymnasiearbete
Här finns den stora mängden data för gymnasiearbetet i formaten json. Det är två filer som praktiskt taget innehållet samma data, med skillnader i hur testers resultat sammanställs - authentication_scores och authentications_score_cc. Den senare filen har datan kompilerad enligt klass, eller person.
# Hur har datan sammanställts
fingeravtrycksbilder har hämtats från 'The Second International Competition for Fingerprint Verification Algorithms' DB2 och kan hittas [här](http://bias.csr.unibo.it/fvc2002/databases.asp). Bilderna matades in i nio olika komprimeringslösningar, dessa är: 
1. DCT med pixel-binning och quantization kernel 8x8
⋅⋅* Kerneln är populerad med samma värden som förekommer i JPEG standarden.
2. FFT med pixel-binning
..* 8x8 delmatriser matades in i algoritmen åt gången
3. Kontinuerlig FFT
4. Low Pass FFT algorithm
..* skärmar av de höga koefficienterna av den diskreta FT
5. High Pass FFT algorithm
..* skärmar av de mindre koefficienterna av den diskreta FT
6. Low Pass Butterworth
7. High Pass Butterworth
8. Low Pass Gaussian Blur
9. High Pass Gaussian Blur

så ser ut produkten av de sex sista algoritmerna (produkten av de första tre är inget speciellt, utan 'vanlig' komprimering)
![sample images](https://github.com/adam-p/markdown-here/raw/master/src/common/images/icon48.png "bilderna förekommer i samma ordning som algortimerna i listan ovan")

Produkter av algoritmerna, tillsammans med den orginiella bilden mätas sedan in och testas med fingeravtryck-verifiering algoritmen mot alla fingeravtryck i databasen. Datan sammanställs i python dicts som sedan dumpas som json i externa filer.

# För att tydliggöra hur datan har kommit till går vi igenom viktigaste askpekter av algoritmen
## definationen 'class', 'length' och 'threshold'
... databasen DB2 består av 8 fingeravtryck per person för åtta personer, varav vi använder endast 4 avtryck per person för 4 personer. programmet använder opencv ORB, som är en algoritm med hjälp av maskininlärning hittar och kategoriserar punkter i en given bild. Opencvs implementering har också en funktion .math() som matchar punkter inom ett visst tröskelvärde för avstånd, här benämnas 'threshold', mängden av punkterna som sparas i listan, är längden av listan och motsvarar 'length' i algoritmen. Denna lösning är mycket smart och räknar med skillnader i rotation, relativ intensititet i fingeravtryckens åsar. 

med hjälp av length och threshold, går det att räkna sannolikheten att ett avtryck identifieras tillhöra den riktiga personen, och vilken bild som är närmast, är bilden närmare till andra bilder tillhörande samma klass eller bilder av andra klasser. Frågeställningen "Vilken av metoderna DCT samt FFT ger högst kvalité på svartvita foton av mina fingeravtryck vid samma filstorlek?" har alltså utvidgats till att vara en jämförelse av 9 lösningar till komprimering. Men, ordet 'kvalite' behöver också andra mått, än sannolikhet att bilden ska identifieras, dessa mått kommer i formen av ssim, och nrmse, enkla algoritmer som 'objektivt' ger ett mått på hur nära den komprimerade bilden är till originella bilden. Data för dessa presenteras i filen 'scores.json'


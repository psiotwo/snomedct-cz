## SNOMED-CT - užití, postupy, nástroje

[SNOMED-CT](http://www.snomed.org/snomed-ct/five-step-briefing) je terminologie nemocí, vyšetření a dalších klinických konceptů. Jedná se o rozsáhlý systém, jehož mezinárodní verze obsahuje cca 300k různých pojmů. SNOMED-CT dosud není k dispozici v českém jazyce, základní verze i většina rozšíření je k dispozici v angličtině. Může plnit několik rolí, od prosté encyklopedie, až po nástroj k integraci elektronických zdravotních záznamů (EHR). 

Zde budou představeny pouze některé technické scénáře práce se SNOMED-CT. Čtenáře neznalého SNOMED-CT odkazuji na 
- úvodník na Wikipedii o [SNOMED-CT](https://en.wikipedia.org/wiki/SNOMED_CT)
- [vzdělávací zdroje organizace Snomed International](https://www.snomed.org/snomed-ct/education). Velmi dobrý přehled dává SNOMED Foundational Course, který je nabízen zdarma.
- informace o stavu implementace SNOMED-CT v České republice na [webu ÚZIS](https://www.uzis.cz/index.php?pg=registry-sber-dat--klasifikace--snomed-ct).

### Koncepty SNOMED-CT
Terminologii SNOMED-CT si lze představit jako rozsáhlou "síť" (znalostní graf) tzv. konceptů a jejich vzájemných vazeb. Příkladem konceptu je "bolest levé končetiny". Koncepty však nejsou pouhá slova či sousloví - ta by sama o sobě mohla být nejednoznačná a interpretovatelná různými lidmi různě. Koncepty tedy popisují *úplné  význam* -- proto kromě sousloví samotného, jsou identifikovány samostatnými číselnými identifikátory, opatřeny synonymy, a to i v různých jazycích, sémantickým typem  - všechny tyto informace dále omezují dezinterpretaci. Příklad klíčových částí popisu konceptu "bolest levé nohy" by vypadal takto:

* `287047008` (jednoznačný identifikátor konceptu, tzv. SCTID)
* `Pain in left lower limb` (preferovaný term v britské i americké angličtině)
* `Pain in left leg` (synonymum v britské i americké angličtině)
* `finding` (sémantický typ, určující, že se jedná o klinický nález a nikoliv např. o proceduru nebo část těla)

Kromě těchto základní anotací, může být u konceptu uvedena např. slovní definice.

#### Vazby na ostatní koncepty
Další klíčovou částí popisu konceptu jsou jeho vztahy s ostatními koncepty - těch lze využít jako pro navigaci mezi koncepty např. v encyklopedickém procházení systému, tak i k vytěžování tohoto znalostního grafu. Např. koncept "bolest levé končetiny" je propojen pomocí dvou vazeb s dalšími dvěma koncepty (zápis uvádím pro přehlednost zjednodušený, bez SCTID jednotlivých konceptů):

* `Pain in Left Lower Limb` `Is a` `Pain in Lower Limb` - tato vazba říká že bolest v levé noze je speciálním případem bolesti v noze. Pro to využívá dva jiné SNOMED-CT koncepty - a sice atribut `Is a`, který se používá právě v roli specializační vazby a koncept `Pain in Lower Limb` (SCTID 10601006) označující bolest v noze. Takovéto specializační vazby vytváří poměrně hlubokou taxonomickou strukturu (cca 10 úrovní), od obecných (např. `Clinical Finding`) po velmi konkrétní (např. `Acute arthralgia of knee`). Tato hierarchie usnadňuje navigaci mezi koncepty a rovněž se využívá pro odvozování (viz dále)

* `Pain in Left Lower Limb` `Finding site`  `Structure of left lower limb` - tato vazba říká, že bolest v levé dolní končetině se nalézá na levé dolní končetině. Opět, jak `Finding site`, tak `Structure of left lower limb` jsou SNOMED-CT koncepty. Ač se tato znalost může zdát zbytečná a zřejmá, její zachycení je klíčové např. pro získání všech možných typu klinických nálezů, které se mohou vyskytovat na levé dolní končetině - např. proto, abychom umožnili zdravotnickému personálu popsat obsah dané zdravotní zprávy způsobem, který lze následně využít jak pro inteligentní podporu rozhodování přímo v klinických informačních systémech, tak pro následné analýzy a studie. 

V případě konceptu "bolest levé končetiny" mají tyto dvě vazby navíc speciální status -- jsou tzv. "definiční" a tedy říkají, že kdykoliv máme "bolest v dolní končetině", která se vyskytuje na "levé dolní končetině", lze ji popsat rovněž jako "bolest levé dolní končetiny". Opět, jedná se o znalost sloužící zejména k analytickým účelům.

### Vytěžování SNOMED-CT
Správa takto rozsáhlé taxonomie vyžaduje zjednodušit popis tvorby konceptů. Právě formální definice poskytují silný nástroj, který lze využít pro odvození nových částí hierarchie (`Is a` vazeb). Pro tyto účely se využívá tzv. automatické dokazování v deskripční logice pomocí jazyka [OWL-EL](https://www.w3.org/TR/owl2-profiles/#OWL_2_EL). Proto, ačkoliv v taxonomii SNOMED-CT nejsou explicitně uvedeny žádné koncepty, které by byly specializacemi bolesti v levé dolní končetině (`Is a` `Pain in Left Lower Limb`), pomocí automatické klasifikace je lze odvodit - jedná se např. o koncept `Greater trochanteric pain syndrome of left lower limb`.

Kromě takto jednoduchých vztahů, je možné z předpokládaných typů konceptů a vazeb "konstruovat" složitější dotazy pomocí jazyka Expression Constraint Language (ECL). Např. pro zjištění, které typy paralýzy mohou být způsobeny problémem centrálního nervového systému `<< 29426003 | Paralytic syndrome |: << 42752001 | Due to | = << 246556002 | Central nervous system finding |`. Výsledkem takového dotazu je např. koncept `Acute paralysis due to lesion of spinal cord` (SCTID 698755001).

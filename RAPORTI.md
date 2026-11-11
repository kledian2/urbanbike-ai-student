# Raporti i studentit: UrbanBike AI

Studenti:Kledian Miftari
Grupi (nëse punohet në grup):
Data: 11/11/2026
GitHub-i im: https://github.com/kledian2/urbanbike-ai-student
Figma: https://www.figma.com/design/Fvjo55YIDBdTYUAuo5i02d/UrbanBike-AI-%E2%80%93-Kledian-Miftari?node-id=0-1&t=rVsAc7cvsmmjOc8I-1

## Qëllimi

Problemi që zgjidhim: Sa qira biçikletash priten për ditën pasuese në Washington, D.C.?
Target-i y: rentals (totali ditor i qirave, kolona cnt e datasetit UCI)
Predictorët X: rentals_lag1, rentals_lag7, rentals_mean7, temp_c_lag7, rain_mm_lag7, wind_ms_lag7, trend_days, weekday, month, workingday, holiday
Koha kur parashikojmë dhe çfarë informacioni është i njohur: Parashikimi është njëditor. Dihen qiratë e ditës së djeshme dhe të 7 ditëve më parë, mesatarja e 7 ditëve të kaluara dhe moti i NASA nga 7 ditë më parë. Të dhënat janë historike, 2011–2012.

## Pastrimi

Burimet reale dhe çelësi i bashkimit: UCI Bike Sharing (day.csv, 731 ditë) dhe NASA POWER (nasa_weather.csv, 731 ditë). Bashkimi bëhet sipas kolonës date.
Dallimi mes origjinaleve dhe kopjes didaktike: Burimet origjinale nuk kishin mungesa. Problemet u injektuan qëllimisht vetëm te kopja didaktike data/teaching/dirty.csv: 18 vlera temp_c bosh, 12 vlera rain_mm bosh, 6 tekste "gabim" te temp_c dhe 12 rreshta të dublikuar.
Rreshtat para/pas: 743 rreshta para pastrimit, 731 pas pastrimit. Pas krijimit të lag-eve mbeten 724 rreshta për modelim.
Dublimet: U gjetën 12 dublime identike dhe u hoqën me drop_duplicates, kështu data është unike dhe mbeten 731 ditë.
Mungesat para/pas konvertimit: Para konvertimit kishte 30 qeliza boshe. Teksti "gabim" u kthye në NaN me pd.to_numeric(errors="coerce"), prandaj mungesat meteorologjike janë 36 (30 boshllëqe + 6 tekste).
Arsyeja e imputimit vetëm me train: Mungesat plotësohen brenda Pipeline me medianën e mësuar vetëm nga train. Kështu validation dhe testi nuk ndikojnë në mësim (nuk ka rrjedhje të dhënash nga e ardhmja). Mungesa nuk trajtohet si 0°C, sepse do të ishte vlerë e rreme.

## Grafikët

Vëzhgimi 1 dhe figura përkatëse (01_demand.png): Qiratë ditore rriten nga rreth 1000–2000 në janar 2011 në rreth 4000–5000 nga pranvera deri në vjeshtë 2011, ulen në dimër (rreth 2500–3500) dhe rriten sërish në pranverën 2012, me vlera mbi 6000. Mesatarja e 7 ditëve e zbut luhatjen e madhe ditore.
Vëzhgimi 2 dhe figura përkatëse (02_workingday.png): Mesatarja e qirave në ditët e punës është pak më e lartë (rreth 3750) se në fundjavë/festa (rreth 3600). Dallimi është i vogël dhe mesatarja nuk tregon gjithë shpërndarjen.
Pse një lidhje e dukshme nuk provon shkakësi: Te 03_weather.png qiratë priren të rriten me temperaturën nga rreth -5°C deri në 15–20°C, por pikat janë shumë të shpërndara. Kjo është lidhje vizuale. Ndikojnë edhe rritja e shërbimit me kohën dhe stina, prandaj nuk mund të themi se temperatura e shkakton rritjen.

## Vlerësimi

| Modeli | Validation MAE | Test MAE | Test RMSE | Test R² |
|---|---:|---:|---:|---:|
| Yesterday | 775.203 | 916.402 | 1330.386 | 0.539 |
| DummyMean | 2953.866 | 2494.628 | 2790.064 | -1.027 |
| Ridge | 687.558 | 893.251 | 1252.655 | 0.591 |
| RandomForest | 1821.528 | 1671.499 | 2042.654 | -0.087 |

Modeli i zgjedhur: Ridge, sepse ka MAE-n më të ulët në validation mes dy kandidatëve ML (687.558 kundrejt 1821.528 për RandomForest). Zgjedhja u bë para se të interpretohej testi.
Rezultati në test: Ridge ka MAE 893.251 qira në ditë, rreth 2.5% më i ulët se baseline-i Yesterday (916.402). Është përmirësim i vogël dhe nuk pretendoj domethënie statistikore.
Çfarë ndodhi me Random Forest: Ishte më i keq se Ridge dhe se baseline-i Yesterday (test MAE 1671.499, R² -0.087). Një model më kompleks nuk është automatikisht më i mirë. Këtu nuk e përgjithësoi mirë, sepse kërkesa në periudhën e testit është më e lartë se ajo e mësuar në train dhe pemët nuk e nxjerrin vlerën jashtë intervalit të train.

## Kufizimet

1. Dataset i një qyteti (Washington, D.C.) dhe i një periudhe historike (2011–2012). Rezultatet nuk vlejnë automatikisht për qytete ose vite të tjera.
2. Të dhënat meteorologjike të NASA vijnë nga rrjeti meteorologjik (reanalizë) dhe LST, prandaj nuk janë matje direkte në stacion.
3. Parashikimi njëditor supozon se qiratë e ditës së djeshme janë të njohura. Nuk është shërbim i testuar për kërkesën e sotme dhe lag7 nuk i zgjidh të gjitha kufizimet e të dhënave historike.

## Kontributet individuale

- requirements.txt dhe .venv: Krijova mjedisin virtual me Python 3.12. Instalimi dështoi me Python 3.15 sepse numpy nuk kishte paketë të gatshme, prandaj vendosa të përdor versionin 3.12 që kërkon projekti.
- 01_clean.py: Ekzekutova pastrimin dhe e verifikova me reports/data_quality.json (731 → 743 → 731 rreshta, 36 mungesa).
- 02_visualize.py: Interpretova tri grafikët dhe shkrova dy vëzhgimet te seksioni Grafikët.
- 03_train.py: Krahasova katër modele dhe e zgjodha Ridge nga validation MAE.
- app.py: Nisa aplikacionin lokal dhe provova parashikimin për datën 2012-09-01.

## Burimet

UCI: https://doi.org/10.24432/C5W894
NASA: https://power.larc.nasa.gov/docs/services/api/temporal/daily/
Provenienca lokale: data/raw/provenance.json

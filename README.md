# 🏦 Home Credit Default Risk

![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)
![Pandas](https://img.shields.io/badge/pandas-%23150458.svg?style=for-the-badge&logo=pandas&logoColor=white)
![XGBoost](https://img.shields.io/badge/XGBoost-337AB7?style=for-the-badge&logo=xgboost&logoColor=white)
![Optuna](https://img.shields.io/badge/Optuna-6C63FF?style=for-the-badge&logo=optuna&logoColor=white)
![SHAP](https://img.shields.io/badge/SHAP-FF6B6B?style=for-the-badge)
![Kaggle](https://img.shields.io/badge/Kaggle-20BEFF?style=for-the-badge&logo=Kaggle&logoColor=white)

Modello predittivo per identificare i clienti ad alto rischio di default su un prestito,
a supporto delle decisioni di erogazione del credito.

🔗 **Notebook su Kaggle:** [View here](https://www.kaggle.com/code/lucalullo/home-credit-default-risk)  
📓 **Notebook su GitHub:** [View here](https://github.com/lucalullo/Home-Credit-Default-Risk/blob/main/home-credit-default-risk.ipynb)

---

## 📌 Obiettivo

Il dataset contiene **307.511 clienti** e **120 feature** che descrivono lo snapshot al momento della richiesta di prestito.
La variabile target è binaria: **1 = default**, **0 = non default**.
Il dataset è fortemente sbilanciato — circa **91.9% non-default** vs **8.1% default** —
rendendo accuracy una metrica inutile e Recall, F1 e AUC-ROC le metriche di riferimento.

---

## 📈 Performance

| Metrica | Valore |
|---------|--------|
| **Model** | XGBoost Classifier |
| **Ottimizzazione** | Optuna — 50 trial |
| **Metrica target** | AUC-ROC |
| **Soglia** | Ottimizzata su Precision-Recall curve (Precision minima 20%) |

---

## 🔄 Pipeline completa

Il progetto segue il ciclo di vita completo del dato in 7 step:

1. Esplorazione e comprensione dei dati
2. Pre-processing e gestione dei valori mancanti
3. Valutazione e gestione dello sbilanciamento delle classi
4. Confronto di modelli con valutazione
5. Feature engineering da 6 sorgenti dati esterne
6. Ottimizzazione degli iperparametri con Optuna
7. Interpretabilità con SHAP e risposta aziendale

---

## 🛠️ Key Features

### Data Cleaning
- Rimozione manuale delle colonne irrilevanti o con troppi valori mancanti
- Gestione del placeholder `DAYS_EMPLOYED = 365243` (pensionati/disoccupati → `NaN`)
- Imputation con **mediana** per colonne numeriche e **moda** per categoriche
- One-Hot Encoding con `drop_first=True` per evitare multicollinearità
- StandardScaler fittato solo sul train set per evitare data leakage

### Gestione dello Sbilanciamento
Confronto tra tre approcci su Logistic Regression:

| Metodo | Pro | Contro |
|--------|-----|--------|
| **Class Weight** ✅ | Nessuna modifica ai dati | — |
| SMOTE | Crea clienti sintetici | Instabile ad alta dimensionalità |
| Undersampling | Semplice | Butta via dati reali |

### Confronto Modelli

| Modello | Note |
|---------|------|
| Logistic Regression | Baseline lineare, interpretabile |
| Extra Trees | Scartato per calibrazione delle probabilità scarsa |
| **XGBoost** ✅ | Selezionato — AUC-ROC equivalente a LR, maggiore potenziale dopo feature engineering |

### Feature Engineering — 6 Sorgenti Esterne

| File | Informazione aggiunta |
|------|-----------------------|
| `bureau.csv` | Storico crediti con altre istituzioni |
| `previous_application.csv` | Domande di prestito precedenti con Home Credit |
| `installments_payments.csv` | Puntualità nei pagamenti a rate |
| `POS_CASH_balance.csv` | Prestiti POS e cash attivi/completati |
| `credit_card_balance.csv` | Utilizzo e ritardi su carte di credito |
| `bureau_balance.csv` | Comportamento mensile sui crediti con terze parti |

Ogni sorgente è validata con AUC-ROC incrementale su XGBoost prima di procedere.

### Ottimizzazione con Optuna
- **50 trial** su validation set separato (test set intoccato fino alla valutazione finale)
- Iperparametri ottimizzati: `n_estimators`, `max_depth`, `learning_rate`, `subsample`, `colsample_bytree`, `min_child_weight`
- Soglia ottimizzata sulla Precision-Recall curve con vincolo `Precision ≥ 20%`

### Interpretabilità con SHAP
- `TreeExplainer` — esatto e non approssimato per XGBoost
- **Bar chart** — importanza media delle feature sulla popolazione
- **Beeswarm plot** — impatto e direzione per ogni feature su ogni cliente
- **Waterfall plot** — spiegazione per il singolo cliente a rischio
- **Report aziendale** testuale con fattori di rischio, fattori protettivi e raccomandazione operativa

### Feature più Importanti

| Feature | Significato | Direzione rischio |
|---------|-------------|-------------------|
| `ext_source_2/3` | Rating creditizio esterno | Basso → più rischio |
| `prev_mean_payments` | Rate medie prestiti precedenti | Alte → più rischio |
| `inst_late_rate` | Percentuale rate in ritardo | Alta → molto più rischio |
| `bureau_debt_ratio` | Rapporto debito/credito su terzi | Alto → più rischio |
| `days_employed` | Anzianità lavorativa | Bassa → più rischio |
| `days_birth` | Età del cliente | Giovane → più rischio |

---

## 🚀 How to use

1. Clona il repository:
```bash
   git clone https://github.com/lucalullo/Home-Credit-Default-Risk.git
```

2. Installa le dipendenze:
```bash
   pip install pandas numpy scikit-learn xgboost imbalanced-learn optuna shap matplotlib seaborn
```

3. Scarica i dati dalla competition Kaggle:
   [Home Credit Default Risk](https://www.kaggle.com/competitions/home-credit-default-risk/data)

4. Esegui il notebook `home-credit-default-risk.ipynb`

---

**Author:** [Luca Lullo](https://github.com/lucalullo)  
*Data Scientist | Machine Learning Applied*

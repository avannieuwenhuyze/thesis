
## PINN et enrichissement du graphe

Le modèle PINN permet d'enrichir le graphe avec  le $\hat{N}$ prédit (noeud  TemporalPattern)

## Ajout de la température

![[figures/Pasted image 20260619174718.png|399]]

Le patient a un évènement clinique de type body_temperature 

`ex:patient_10007818 a :PATIENT ;`
    `rdfs:label "Patient 10007818" ;`
    `:hasClinicalEvent ex:body_temperature_10007818_32359580_01_001,`
        `ex:body_temperature_10007818_32359580_01_002,`
        `ex:body_temperature_10007818_32359580_01_003,`
        `ex:body_temperature_10007818_32359580_01_004,`
        `ex:body_temperature_10007818_32359580_01_005,`
        `ex:body_temperature_10007818_32359580_01_006,`
        `ex:body_temperature_10007818_32359580_01_007,`
        `ex:body_temperature_10007818_32359580_01_008,`
        `ex:body_temperature_10007818_32359580_01_009,`
        `ex:body_temperature_10007818_32359580_01_010,`
        `ex:body_temperature_10007818_32359580_01_011,`
        `ex:body_temperature_10007818_32359580_01_012,`
        `ex:body_temperature_10007818_32359580_01_013,`
        `ex:body_temperature_10007818_32359580_01_014,`
        `ex:body_temperature_10007818_32359580_01_015,`
        `ex:body_temperature_10007818_32359580_01_016,`
        `ex:body_temperature_10007818_32359580_01_017,`
        `ex:body_temperature_10007818_32359580_01_018,`
        `ex:body_temperature_10007818_32359580_01_019,`


La température est une sous classe de CLINICAL_EVENT qui utilise TEMPORAL_INSTANT (qui étend TEMPORAl_INSTANT de OWLTime)

![[figures/Pasted image 20260619175129.png]]

`ex:body_temperature_10007818_32359580_01_002 a :BODY_TEMPERATURE ;`
    `rdfs:label "Body temperature 35.00 °C" ;`
    `:hasBodyTemperatureValue 3.5e+01 ;`
    `:occursAt ex:t_10007818_32359580_01_temp_002 .`



![[figures/Pasted image 20260619172305.png]]






![[figures/Pasted image 20260619153128.png]]
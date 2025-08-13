1. Resumen Ejecutivo
Se desarrolló un pipeline robusto para predecir la cancelación de clientes utilizando técnicas de machine learning y análisis interpretativo. El modelo ganador fue la Regresión Logística (LR), entrenada en un Pipeline con preprocesamiento por tipo de variable e inferencia probabilística estable; se seleccionó por su mejor AUC ROC y PR-AUC frente a alternativas (Random Forest y Árbol de Decisión), además de su interpretabilidad vía coeficientes y su estabilidad operativa. Este informe sintetiza los principales hallazgos y sugiere acciones de retención alineadas con los factores clave de abandono.

Nota: el anexo incluye ajuste de umbral (F2), matrices de confusión, curvas ROC/PR, gains/lift, calibración y árbol interpretable.

2. Estructura del proyecto
✅ Análisis exploratorio y visual (boxplots numéricas vs abandono)

✅ Codificación (One-Hot) e imputación por tipo de dato

✅ Manejo del desbalance con class_weight (sin SMOTE para evitar fuga de datos)

✅ Estandarización de numéricas (StandardScaler, compatible con matrices dispersas)

✅ Entrenamiento de modelos (LR, RF, Árbol de Decisión)

✅ Ajuste de umbral orientado a negocio (optimización F2)

✅ Interpretabilidad: coeficientes (LR), Árbol interpretable y Permutation Importance (RF)

✅ Evaluación: reportes de clasificación, AUC ROC, PR-AUC, matrices de confusión, curvas ROC/PR

✅ GridSearchCV disponible como bloques listos para ejecutar (comentados), manteniendo el estilo tradicional del cuaderno

Recomendación de saneamiento: excluir identificadores de cliente (p. ej., id_cliente) del modelado para evitar sobreajuste por alta cardinalidad. Se observan coeficientes espurios cuando se codifica esta variable.

3. Principales Factores que Influyen en la Cancelación
Con base en coeficientes de LR (signo/magnitud), Permutation Importance (RF) y boxplots, se identificaron los siguientes factores como más influyentes:

🔺 Factores que AUMENTAN la probabilidad de abandono

| Variable / Categoría                 | Evidencia técnica                               | Interpretación operativa                             |
| ------------------------------------ | ----------------------------------------------- | ---------------------------------------------------- |
| **Contrato: month-to-month**         | Coef. LR positivo; alta importancia en RF       | Menor compromiso → mayor propensión a churn          |
| **Internet: Fiber optic**            | Coef. LR positivo                               | Expectativas altas / sensibilidad a incidencias      |
| **Método de pago: electronic check** | Coef. LR positivo                               | Menor fidelización/automatización                    |
| **Sin soporte/seguridad online**     | Coef. LR positivo                               | Menor valor percibido y potencial insatisfacción     |
| **Cargos totales altos**             | Alta importancia en RF; relación positiva en LR | Acumulación de cobros asociada a fricción o reclamos |


🟢 Factores que DISMINUYEN la probabilidad de abandono

| Variable / Categoría                        | Evidencia técnica                             | Interpretación operativa                |
| ------------------------------------------- | --------------------------------------------- | --------------------------------------- |
| **Contrato: two year / one year**           | Coef. LR negativo; alta importancia en RF     | Contratos de mayor plazo retienen mejor |
| **Mayor antigüedad (`meses_en_empresa`)**   | Coef. LR negativo (fuerte); importancia en RF | Clientes fieles con menor riesgo        |
| **Soporte técnico / servicios adicionales** | Coef. LR negativo (categorías “Yes”)          | Mejora experiencia y valor percibido    |
| **Internet: DSL / sin internet**            | Coef. LR negativo vs. “Fiber optic”           | Segmentos con expectativas/uso menor    |


Nota: el efecto de cargos_mensuales puede verse enmascarado por contrato y servicios. Recomendado analizar por segmentos (p. ej., solo mes a mes).

4. Comparativa de Modelos
Evaluación en holdout con umbral 0.5 (los umbrales óptimos por F2 se reportan en el notebook):

| Modelo                  | Accuracy   | Precision  | Recall     | F1 Score   | AUC        |
| ----------------------- | ---------- | ---------- | ---------- | ---------- | ---------- |
| **Regresión Logística** | **0.7645** | 0.5294     | **0.7701** | **0.6275** | **0.8375** |
| Random Forest           | **0.7879** | **0.6093** | 0.4920     | 0.5444     | 0.8232     |
| Árbol de Decisión       | 0.7300     | 0.4855     | 0.8075     | 0.6064     | 0.8268     |



📌 Modelo ganador: Regresión Logística por mejor AUC ROC y PR-AUC, y trade-off favorable entre recall y estabilidad. RF ofrece mayor precision a 0.5 (útil si se penaliza fuertemente el FP), y el Árbol aporta reglas claras para negocio.

5. Estrategias de Retención Propuestas
🎯 Segmentación Proactiva Alta prioridad: clientes month-to-month, con internet fiber optic y sin servicios de soporte/seguridad.

Ofrecer upgrades a contratos de 1–2 años con beneficios (descuentos, instalación, equipamiento).

🤖 Automatización de Pagos Incentivar pago automático (tarjeta/transferencia).

Migrar electronic check hacia métodos automáticos (bonos de descuento/GB extra).

🛡️ Promoción de Servicios Adicionales Bundles de soporte técnico + seguridad/respaldo con pruebas gratuitas (30–60 días).

⏳ Programas de Lealtad por Antigüedad Reconocer meses en la compañía (tiers de lealtad, upgrades graduales).

📈 Cargos Altos / Totales Contacto proactivo y encuestas NPS para clientes con cargos totales altos; detectar early signals de insatisfacción.

Operativización recomendada: usar el umbral F2 del notebook para maximizar recall en campañas de prevención, y combinarlo con Gains/Lift para dimensionar el % de clientes a intervenir.

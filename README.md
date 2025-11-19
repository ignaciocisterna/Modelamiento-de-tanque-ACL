# Modelamiento de tanque AETCL

El presente repositorio contiene un código de python que modela el funcionamiento de un tanque de Almacenamiento de Energía Térmica de Calor Latente (AETCL) que contiene un lecho empacado con cápsulas de Material de Cambio de Fase (PCM por sus siglas en inglés), basándose en el artículo de Klitou et al. (https://www.tandfonline.com/doi/full/10.1080/14786451.2024.2306416).

Este tipo de tecnología es especialmente relevante para aplicaciones donde existe un desfase temporal entre la disponibilidad de energía térmica (por ejemplo, proveniente de colectores solares o calor residual de procesos) y su demanda efectiva.Los sistemas de AETCL permiten almacenar grandes cantidades de energía en el proceso de fusión/solidificación del PCM, ofreciendo alta densidad energética y estabilidad de operación.

Modelar el comportamiento de estos sistemas es clave para comprender el impacto de diferentes parámetros de diseño como las dimensiones del sistema, la porosidad del lecho, la velocidad del fluido de transferencia de calor, la selección del PCM, entre otros; permitiendo diseñar sistemas más eficientes y adaptados a aplicaciones reales. El código de este repositorio busca precisamente entregar una herramienta transparente y reproducible para analizar el comportamiento de este tipo de unidades.

## Principio de operación

El principio de operación del sistema de estudio se basa hacer circular un fluido de transferencia de calor (HTF por sus siglas en inglés) a través de un lecho empacado con cápsulas que contienen un PCM. El sistema se carga cuando el HTF ingresa más caliente que la temperatura del lecho (y de fusión del PCM), el calor de esta corriente se transfiere a las cápsulas fundiendo el PCM, almacenando una gran cantidad de energía en forma de calor latente. Durante la descarga, el proceso se invierte: el HTF circula más frío que el PCM fundido, solidificándolo y liberando el calor previamente almacenado.

### Ecuaciones dominates

#### Balance de energía HTF (ec.1)

<img width="404" height="44" alt="CodeCogsEqn (3)" src="https://github.com/user-attachments/assets/4ed07d7a-6b70-4206-8aee-fea3683acc8a" />

#### Balance de Energía PCM (ec.2)

<img width="354" height="44" alt="CodeCogsEqn (2)" src="https://github.com/user-attachments/assets/f9ec912b-63f7-4ba4-beb3-a35fc6d45c93" />

#### Correlación para cálculo de coeficiente de transferencia de calor en superficie (ec.3)

<img width="253" height="43" alt="CodeCogsEqn (4)" src="https://github.com/user-attachments/assets/2caac588-54b5-4441-a1d2-6bc97e136d0b" />

#### Calor específico efectivo del PCM (*)

<img width="279" height="38" alt="CodeCogsEqn (5)" src="https://github.com/user-attachments/assets/26ea0261-32a1-4588-bd44-89abacf5ba4b" />

#### Aproximación de la fracción líquida del PCM (ec.4) (**)

<img width="420" height="32" alt="lagrida_latex_editor" src="https://github.com/user-attachments/assets/017d4264-4c2f-4c42-980b-2639a6cbb090" />


(*) El resto de propiedades efectivas del PCM siguen la misma ponderación según la fracción líquida sin incluir términos adicionales por cambio de fase (como el calor latente L).

(**) Durante cambio de fase ocurrida en el intervalo específicado, 0 para T menores y 1 para T mayores, DeltaT se ajustó según artículo de referencia.

### Sistema de estudio

El sistema modelado corresponde a un tanque AETCL que recibe una corriente cíclica de agua (HTF), la cual es calentada por un colector solar con un aporte energético constante a la corriente (Qu), la siguiente figura adaptada de Klitou et al. representa al sistema en cuestión:

<img width="500" height="344" alt="Sistema LHTES" src="https://github.com/user-attachments/assets/cb290eb6-3568-4e4c-b641-2c20819fab17" />

Bajo estas condiciones los fujos de entrada y salida del tanque quedan realcionados por medio de la siguiente ecuación (ec.5):

<img width="197" height="52" alt="lagrida_latex_editor (1)" src="https://github.com/user-attachments/assets/04b9d6b8-1c79-4d8f-be5f-15f172c6519b" />   

Consideración adicional: el sistema comienza a una temperatura inicial de 25°C (298.15 K).

## Implementación a código

En la primera sección del código se pueden modificar los parámetros de diseño del sistema y las propiedades en estado sólido y líquido del PCM y el HTF.

El modelo fue discretizado en la dimensión del largo del tanque (z) por método de líneas y resuelto como un sistema de Ecuaciones Diferenciales Ordinarias con el solver solve_ivp() del la librería scipy.integrate.

En la sección de # Extracción de resultados, se puede modificar el vector pos_frac para obtener uno o más perfiles temporales de temperatura a la fracción(es) del largo del tanque que se indiquen (desde entrada en 0 a salida en 1).

En la última sección del código se grafican los perfiles extraídos previamente, con la posibilidad de comparar contra los perfiles de referncia utilizados para ajustar parámetros.

## Notas finales

Este repositorio también incluye el código con el cual se ajustaron parámetros para el código de modelación, para lo cual se utilizó minimización de diferencias cuadradas.

Para modelar la descarga en este sistema, se debe modificar la temperatura inicial del sistema a las condiciones iniciales deseadas y reemplazar la condición de recirculación con adición energética (ec.5) por una temperatura constante de entrada del fluido a calentar.

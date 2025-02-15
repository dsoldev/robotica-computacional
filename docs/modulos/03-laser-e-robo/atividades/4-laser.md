# Entendendo o Sensor Laser

Nesta atividade vamos explorar o tópico do sensor laser, `scan`. Este sensor se encontra no topo do robô e é utilizado para detectar obstáculos no ambiente.

Para entender como o sensor funciona, vamos primeiro ver o tipo de mensagem que é enviado no tópico `scan`. Para isso, abra um novo terminal e digite:

```bash
ros2 topic info /scan
```

O tipo da mensagem é `sensor_msgs/msg/LaserScan`, que é um tipo de mensagem padrão para sensores laser. Para ver o conteúdo da mensagem, digite:

```bash
ros2 interface show sensor_msgs/msg/LaserScan
```

Agora utilize o comando `echo` para ver o conteúdo do tópico `scan`:

```bash
ros2 topic echo /scan
```

Um exemplo de mensagem é mostrado abaixo:

```bash
header: 
  seq: 7
  stamp: 
    secs: 808
    nsecs: 154000000
  frame_id: "base_scan"
angle_min: 0.0
angle_max: 6.28318977355957
angle_increment: 0.017501922324299812
time_increment: 0.0
scan_time: 0.0
range_min: 0.11999999731779099
range_max: 3.5
ranges: [inf, inf, inf, ...]
intensities: [...]
```
A mensagem no exemplo acima foi cortada por ser muito grande.

A mensagem do sensor laser é composta pelos seguintes campos:

* `header`: Cabeçalho da mensagem, que contém informações como o tempo de envio da mensagem e o frame de referência.

* `angle_min: 0.0`: Ângulo inicial do sensor. O valor `0.0` corresponde a leiura do sensor diretamente para frente do robô.

* `angle_max: 6.28...`: Ângulo final do sensor. O valor `6.28...` equivale a uma volta completa (360 graus).

* `angle_increment: 0.017...`: Incremento angular entre cada leitura do sensor. O valor `0.017...` equivale a um ângulo de 1 grau.

* `scan_time & time_increment: 0.0`: Tempo de varredura do sensor e tempo entre cada leitura. O valor `0.0` indica que o sensor está configurado para enviar as leituras o mais rápido possível.

* `range_min: 0.119...` [m]: Distância mínima que o sensor consegue detectar. O valor `0.119...` equivale a 11.9 cm.

* `range_max: 3.5` [m]: Distância máxima que o sensor consegue detectar. O valor `3.5` equivale a 3.5 m.

* `ranges: [inf, inf, inf, ...]`: Vetor com as leituras do sensor. O tamanho do vetor é igual a `angle_max/angle_increment`, ou seja, a lista de leituras é composta por 360 elementos que representam as leituras do sensor a cada 1 grau. O valor `inf` indica que o sensor não detectou nada naquela direção.

* `intensities: [...]`: Vetor com as intensidades das leituras do sensor. Nosso sensor não possui essa informação, portanto, pode desconsiderar esse campo.

Portanto, no valor `ranges`, o sensor retonar um vetor de 360 elementos, que representam as leituras da distância do sensor a cada 1 grau. O valor faz medições no sentido anti-horário, começando de frente para o robô. Na **simulação**, valor `inf` indica que o sensor não detectou nada naquela direção, no **robô real**, o valor `0` indica que o sensor não conseguiu fazer a leitura.

**Pergunta:** Qual o indice do vetor `ranges` que representa a leitura do sensor diretamente para frente do robô? E da esquerda? E da direita? E para trás?

## Módulo de Odometria - APS 3

Vamos criar um módulo para encapsular a leitura do sensor laser, que possa ser facilmente importado em outros programas.

Crie um arquivo denominado `laser.py` e uma classe chamada `Laser` sem herança. Essa classe deve:

* definir um subscriver para o tópico `laser` que chama a função `laser_callback` quando uma mensagem é recebida.

* definir uma função `laser_callback` que recebe uma mensagem do tipo `sensor_msgs/msg/LaserScan` e armazena os seguintes parâmetros:
    * Utilize o seguinte comando para converter a lista em um array numpy:
    ```python
    self.laser_msg = np.array(msg.ranges).round(decimals=2) 
    ```

    * Utilize o seguinte comando jogar os valores `0` para `inf`, removendo ambiguidades:
    ```python
    self.laser_msg[self.laser_msg == 0] = np.inf
    ```

    * Converta self.laser_msg para uma lista novamente.

    * Pegue +- 5 valores na frente do robô e armazene na variável `self.front`.

    * Pegue +- 5 valores na esquerda do robô e armazene na variável `self.left`.

    * Pegue +- 5 valores na direita do robô e armazene na variável `self.right`.

    * Pegue +- 5 valores atrás do robô e armazene na variável `self.back`.

Agora siga as instruções da APS 3 para criar um programa que utilize o módulo do sensor laser.
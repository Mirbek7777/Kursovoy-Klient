Данная программа клиент предназначенна для подключению к серверу, отправки сообщений другим участникам чата и приема сообщений.

Код представлен классом Client, вложенным классом inputHandler и классом Logger.

Класс Client
Имеет 5 полей:

private Socket client: клиентский сокет
private BufferedReader in: поток чтения
private PrintWriter out: поток записи
private boolean done: булевское выражение, используется для прерывания
Logger logger: логгер
Класс имплементирует интерфейс раннабл, соответственно в нем реализован метод ран:

@Override
    public void run() {

        try {
            client = new Socket("localhost", getPort());
            out = new PrintWriter(client.getOutputStream(), true);
            in = new BufferedReader(new InputStreamReader(client.getInputStream()));
            logger = new Logger();

            InputHandler inHandler = new InputHandler();
            Thread t = new Thread(inHandler);
            t.start();

            String inMessage;
            while ((inMessage = in.readLine()) != null){
                System.out.println(inMessage);
                logger.log(inMessage);
            }
        } catch (IOException e) {
            shutdown();
        }
    }
Здесь создаем новый клиентский сокет с локальным хостом и портом полученным из файла посредством метода гетпорт, создаем потоки ввода/вывода и логгер, далее создаем обьект обработчика сообщений вводимых клиентом с консоли, обработчик сообщений также реализует интерфейс раннабл, поэтому создаем новый поток и передаем в него логику метода ран этого класса и запускаем этот поток. Создаем обьект строки. Создаем цикл в условии инициализируем обьект строки входящим сообщением если оно есть. Выводим поступившее сообщение от другого участника чата в консоль и логгируем. В случае возникновения исключения выполняется метод выключения в котором меняем булеевское выражение дан(дело) на утвердительное для выхода из цикла ран класса обработчика сообщений, закрываем потоки ввода/вывода, закрываем клиентский сокет.

Метод входа в программу: создаем нового клиента и запускаем его

Класс InputHandler
отправляет вводимые сообщения от клиента в консоль на сервер. Имплементирует интерфейс раннабл, метод ран:

 @Override
        public void run() {
            try {
                BufferedReader inReader = new BufferedReader(new InputStreamReader(System.in));
                while (!done) {
                    String message = inReader.readLine();
                    if (message.equals("/exit")) {
                        out.println(message);
                        inReader.close();
                        shutdown();
                    } else {
                        out.println(message);
                    }
                }
            } catch (IOException e) {
                shutdown();
            }
        }
    }
Создаем новый поток считывающий сообщения от клиента напечатанные в консоль, открываем цикл с условием пока дело не сделанно создаем обькт строки и записываем в него сообщение введеное клиентом, если клиент ввел "выход" отправляем на сервер для того чтобы сервер отключил этот сокет, закрывает поток считавания консоли и выполняем метод выключения. Во всех других случаях отправляем сообщение на сервер, сервер переправляет его уже другим участникам чата.
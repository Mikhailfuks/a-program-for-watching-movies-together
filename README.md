using System;
using System.Net.Sockets;
using System.Text;
using System.Threading;

namespace MovieSync
{
    class Program
    {
        static string serverAddress = "127.0.0.1"; // Адрес сервера (локальный)
        static int port = 8000; // Порт для соединения

        static void Main(string[] args)
        {
            Console.WriteLine("Добро пожаловать в MovieSync!");

            // Выбираем роль: клиент или сервер
            Console.WriteLine("Выберите роль: 1 - Сервер, 2 - Клиент");
            int role = GetChoice();

            if (role == 1)
            {
                StartServer();
            }
            else if (role == 2)
            {
                StartClient();
            }
            else
            {
                Console.WriteLine("Неверный выбор.");
            }

            Console.ReadKey();
        }

        static int GetChoice()
        {
            // ... (Эта функция уже описана в предыдущих примерах)
        }

        static void StartServer()
        {
            try
            {
                // Создаем сокет для прослушивания подключений
                TcpListener serverSocket = new TcpListener(IPAddress.Parse(serverAddress), port);
                serverSocket.Start();
                Console.WriteLine("Сервер запущен. Ожидание подключения...");

                // Ждем подключение клиента
                Socket clientSocket = serverSocket.AcceptSocket();
                Console.WriteLine("Клиент подключен.");

                // Цикл для отправки/получения сообщений
                while (true)
                {
                    // Получаем время воспроизведения от клиента
                    byte[] buffer = new byte[1024];
                    int bytesRead = clientSocket.Receive(buffer);
                    string clientTime = Encoding.ASCII.GetString(buffer, 0, bytesRead);
                    Console.WriteLine($"Получено время от клиента: {clientTime}");

                    // Отправляем сообщение клиенту (например, подтверждение)
                    string message = "Получено!";
                    byte[] data = Encoding.ASCII.GetBytes(message);
                    clientSocket.Send(data);

                    // Delay for demonstration purposes
                    Thread.Sleep(1000); 
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Ошибка: {ex.Message}");
            }
        }

        static void StartClient()
        {
            try
            {
                // Создаем сокет для подключения к серверу
                Socket clientSocket = new Socket(AddressFamily.InterNetwork, SocketType.Stream, ProtocolType.Tcp);
                clientSocket.Connect(serverAddress, port);
                Console.WriteLine("Подключен к серверу.");

                // Цикл для отправки/получения сообщений
                while (true)
                {
                    // Отправляем время воспроизведения на сервер
                    string currentTime = DateTime.Now.ToString("HH:mm:ss");
                    byte[] data = Encoding.ASCII.GetBytes(currentTime);
                    clientSocket.Send(data);
                    Console.WriteLine($"Отправлено время на сервер: {currentTime}");

                    // Получаем сообщение от сервера
                    byte[] buffer = new byte[1024];
                    int bytesRead = clientSocket.Receive(buffer);
                    string serverMessage = Encoding.ASCII.GetString(buffer, 0, bytesRead);
                    Console.WriteLine($"Получено сообщение от сервера: {serverMessage}");

                    // Delay for demonstration purposes
                    Thread.Sleep(1000); 
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Ошибка: {ex.Message}");
            }
        }
    }
}

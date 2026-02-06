
using System;
using MySql.Data.MySqlClient;

namespace MySql1;
static class Program
{
    static void Main()
    {
        Console.OutputEncoding = System.Text.Encoding.Unicode;

        System.Console.WriteLine("оберіть потрібну дію");
        Console.WriteLine("1 - Test DB ");

        Console.WriteLine("2 - Вивести список таблиці ");
        
        Console.WriteLine("3 - Редагувати таблицю");

        Console.WriteLine("4 - Видалити дані в таблиці");

        Console.WriteLine("5 - Додати дані в таблицю");

        Console.WriteLine("0 - Вихід");

        int x = int.Parse(Console.ReadLine());

        switch (x)
        {
            case 1: 
                TestDBConnection(); // Перевірка підключення до БД
                break;
            case 2:
                ListTable();
                break;
            case 3:
                ListTable();
                EditTable();
                ListTable();
                break;
            case 4:
                ListTable();
                DeletePredmet();
                ListTable();
                break;
            case 5:
                AddTable();
                ListTable();
                break;
            case 0:
                Environment.Exit(0);
                break;
            default:

                Console.WriteLine("Невірний вибір. Спробуйте ще раз.");

                break;

        }

        TestDBConnection();

    }
    static void  TestDBConnection()
    {
        var conn = DBUtils.GetDBConnection(); // Отримуємо об'єкт підключення
        conn.Open();
        System.Console.WriteLine("MySQL version : {0}", conn.ServerVersion); // Виводимо версію сервера
        conn.Close();
    }
    static void ListTable() //виводемо лист того що є
    { 
        var conn  = DBUtils.GetDBConnection();
        conn.Open();
        string sql = "SELECT * FROM classrooms";
        var cmd = new MySql.Data.MySqlClient.MySqlCommand(sql, conn);
        var reader = cmd.ExecuteReader();
        while (reader.Read())

        {
            System.Console.WriteLine("ID: {0}, First Name: {1}, Last Name: {2}, Email: {3}",
                reader[0], reader[1], reader[2], reader[3]);
        }

        reader.Close();
        conn.Close();
    }
    static void EditTable() //редагувати
    {
        Console.WriteLine("введіть id");
        int id = int.Parse (Console.ReadLine());    
        var conn = DBUtils.GetDBConnection();
        conn.Open();
        string sql = "SELECT * FROM classrooms WHERE classroom_id = @id" ;
        var cmd = new MySqlCommand(sql, conn);
        cmd.Parameters.AddWithValue ("@id", id);
        var reader = cmd.ExecuteReader();
        Console.WriteLine("дані викладача, змініть потрібні елементи");
        if (!reader.Read())   // Якщо запис не знайдено — вихід
        {
            Console.WriteLine("ID не знайдено.");
            reader.Close();
            conn.Close();
            return;
        }
        Console.WriteLine("Поточні дані:");
        Console.WriteLine("Room: {0}, Building: {1}, Capacity: {2}, Availability: {3}, Equipment: {4}",
            reader["room_number"], reader["building"], reader["capacity"], reader["availability"], reader["equipment"]);

        reader.Close();
        
        Console.WriteLine("введіть id:");
        string room = Console.ReadLine();

        Console.WriteLine("введіть імя викладача:");
        string building = Console.ReadLine();

        Console.WriteLine("введіть скільки йому років:");
        int capacity = int.Parse(Console.ReadLine());

        Console.WriteLine("введіть доступність (Available/Occupied):");
        string availability = Console.ReadLine();

        Console.WriteLine("введіть обладнання:");
        string equipment = Console.ReadLine();
        sql = "UPDATE classrooms SET room_number = @room, building = @building, capacity = @capacity, availability = @availability, equipment = @equipment WHERE classroom_id = @id";
        var cmd2 = new MySqlCommand(sql, conn);
        cmd2.Parameters.AddWithValue("@room", room);
        cmd2.Parameters.AddWithValue("@building", building);
        cmd2.Parameters.AddWithValue("@capacity", capacity);
        cmd2.Parameters.AddWithValue("@availability", availability);
        cmd2.Parameters.AddWithValue("@equipment", equipment);
        cmd2.Parameters.AddWithValue("@id", id);
        cmd2.ExecuteNonQuery();
        conn.Close();
        Console.WriteLine("Дані  оновлено.");
    }
    static void DeletePredmet() //видалити по id
    {
        Console.WriteLine("введіть id предмета:");
        int id = int.Parse(Console.ReadLine());
        var conn = DBUtils.GetDBConnection();
        conn.Open();
        string sql = "DELETE FROM classrooms WHERE classroom_id = @id";
        var cmd = new MySqlCommand(sql, conn);
        cmd.Parameters.AddWithValue("@id", id);
        cmd.ExecuteNonQuery();
        conn.Close();
        Console.WriteLine("id видалено",id);
    }
   

    static void AddTable() //добавити
    {
        var conn = DBUtils.GetDBConnection(); 
        conn.Open();
        Console.WriteLine("введіть id");
        string id = Console.ReadLine();
        Console.WriteLine("введіть іммя преподователя:");
        string room = Console.ReadLine();

        Console.WriteLine("введіть фамілію преподавателя:");
        string building = Console.ReadLine();

        Console.WriteLine("введіть скільки йому років:");
        int capacity = int.Parse(Console.ReadLine());

        Console.WriteLine("введіть доступність (Available/Occupied):");
        string availability = Console.ReadLine();

        Console.WriteLine("введіть обладнання яким він користується:");
        string equipment = Console.ReadLine();

        string sql = "INSERT INTO classrooms (classroom_id, room_number, building, capacity, availability, equipment) " +
                     "VALUES (@id,@room, @building, @capacity, @availability, @equipment)";
        var cmd = new MySqlCommand(sql, conn);
        cmd.Parameters.AddWithValue("@id", id);
        cmd.Parameters.AddWithValue("@room", room);
        cmd.Parameters.AddWithValue("@building", building);
        cmd.Parameters.AddWithValue("@capacity", capacity);
        cmd.Parameters.AddWithValue("@availability", availability);
        cmd.Parameters.AddWithValue("@equipment", equipment);
        cmd.ExecuteNonQuery();

        conn.Close();

    }
    
    } 


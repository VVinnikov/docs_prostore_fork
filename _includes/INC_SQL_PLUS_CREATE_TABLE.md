*   Выполнение запроса недоступно в сервисной базе данных `INFORMATION_SCHEMA`.
*   Имена столбцов должны быть уникальны в рамках логической таблицы.
*   Недопустимо использование зарезервированных имен столбцов: `sys_op`, `sys_from`, `sys_to`, 
    `sys_close_date`, `bucket_id`, `sign`.
*   Первичный ключ должен включать все столбцы ключа шардирования.
*   Ключ шардирования может содержать только целочисленные столбцы.
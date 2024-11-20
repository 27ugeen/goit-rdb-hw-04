-- 1. Створення схеми LibraryManagement
CREATE SCHEMA IF NOT EXISTS LibraryManagement;

-- Використання схеми
USE LibraryManagement;

-- 2. Створення таблиці authors
CREATE TABLE authors (
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    author_name VARCHAR(255) NOT NULL
);

-- 3. Створення таблиці genres
CREATE TABLE genres (
    genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name VARCHAR(255) NOT NULL
);

-- 4. Створення таблиці books
CREATE TABLE books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    publication_year YEAR NOT NULL,
    author_id INT,
    genre_id INT,
    FOREIGN KEY (author_id) REFERENCES authors(author_id) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id) ON DELETE CASCADE ON UPDATE CASCADE
);

-- 5. Створення таблиці users
CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE
);

-- 6. Створення таблиці borrowed_books
CREATE TABLE borrowed_books (
    borrow_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    user_id INT,
    borrow_date DATE NOT NULL,
    return_date DATE,
    FOREIGN KEY (book_id) REFERENCES books(book_id) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE ON UPDATE CASCADE
);

-- 1. Додавання авторів
INSERT INTO authors (author_name)
VALUES 
    ('Dan Simmons'),
    ('Isaac Asimov');

-- 2. Додавання жанрів
INSERT INTO genres (genre_name)
VALUES 
    ('Science Fiction'),
    ('Classic Fiction');

-- 3. Додавання книг
INSERT INTO books (title, publication_year, author_id, genre_id)
VALUES 
    ('Hyperion', 1989, 1, 1),
    ('I, Robot', 1950, 2, 1);

-- 4. Додавання користувачів
INSERT INTO users (username, email)
VALUES 
    ('Tom Cruise', 'tom.cruise@example.com'),
    ('Margot Robbie', 'margot.robbie@example.com');

-- 5. Додавання записів про запозичені книги
INSERT INTO borrowed_books (book_id, user_id, borrow_date, return_date)
VALUES 
    (1, 1, '2024-11-01', '2024-11-20'),
    (2, 2, '2024-11-05', NULL);

-- Створення запиту який об’єднує всі зазначені таблиці за допомогою INNER JOIN
SELECT 
    orders.id AS order_id,
    customers.name AS customer_name,
    employees.first_name AS employee_first_name,
    employees.last_name AS employee_last_name,
    shippers.name AS shipper_name,
    products.name AS product_name,
    categories.name AS category_name,
    suppliers.name AS supplier_name,
    order_details.quantity,
    order_details.id AS order_detail_id
FROM 
    order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id;

-- Підрахунок загальної кількісті рядків у результаті
SELECT COUNT(*) AS row_count
FROM 
    order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id;

-- Створення запиту, який змінює кілька INNER JOIN на LEFT JOIN
SELECT COUNT(*) AS row_count
FROM 
    order_details
LEFT JOIN orders ON order_details.order_id = orders.id
LEFT JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
LEFT JOIN shippers ON orders.shipper_id = shippers.id
LEFT JOIN products ON order_details.product_id = products.id
LEFT JOIN categories ON products.category_id = categories.id
LEFT JOIN suppliers ON products.supplier_id = suppliers.id;

-- LEFT JOIN забезпечує, що всі рядки з таблиці ліворуч (order_details) залишаються, навіть якщо немає відповідного рядка в таблиці праворуч.
-- Порівняння з попереднім результатом:
-- Результат повинен мати більше рядків (або стільки ж), бо LEFT JOIN включає навіть ті рядки, де немає відповідностей у правій таблиці.

-- Створення запиту, який змінює кілька INNER JOIN на RIGHT JOIN
SELECT COUNT(*) AS row_count
FROM 
    order_details
RIGHT JOIN orders ON order_details.order_id = orders.id
RIGHT JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
RIGHT JOIN shippers ON orders.shipper_id = shippers.id
RIGHT JOIN products ON order_details.product_id = products.id
RIGHT JOIN categories ON products.category_id = categories.id
RIGHT JOIN suppliers ON products.supplier_id = suppliers.id;

-- RIGHT JOIN включає всі рядки з таблиці праворуч, навіть якщо немає відповідностей у таблиці ліворуч.
-- Порівняння результатів:
-- Кількість рядків може зрости, якщо є записи в правих таблицях без відповідностей у лівих.

-- Запит із двома LEFT JOIN, двома RIGHT JOIN і рештою INNER JOIN
SELECT COUNT(*) AS row_count
FROM 
    order_details
LEFT JOIN orders ON order_details.order_id = orders.id
LEFT JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
RIGHT JOIN shippers ON orders.shipper_id = shippers.id
RIGHT JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id;

-- Кількість рядків у всіх випадках - 518
-- В базі даних є консистентність даних, і кожен запис у таблиці пов'язаний із відповідними записами в інших таблицях.

-- Вибірка рядків із об’єднання таблиць, де employee_id > 3 та employee_id ≤ 10
SELECT COUNT(*) AS row_count
FROM 
    order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10;

--Кількість рядків після фільтрації з умовою employee_id > 3 та employee_id ≤ 10 становить 317.

-- Групування за категоріями, підрахунок кількості рядків і обчислення середньої кількості товару
SELECT
	categories.name AS category_name,
	COUNT(*) AS row_count,
	AVG(order_details.quantity) AS avg_quantity
FROM 
    order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10
GROUP BY category_name;

-- Фільтрація рядків, де середня кількість товару більша за 21.
SELECT
    categories.name AS category_name,
    COUNT(*) AS row_count,
    AVG(order_details.quantity) AS avg_quantity
FROM 
    order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10
GROUP BY category_name
HAVING avg_quantity > 21;

--Сортування за спаданням кількості рядків.
SELECT
    categories.name AS category_name,
    COUNT(*) AS row_count,
    AVG(order_details.quantity) AS avg_quantity
FROM 
    order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10
GROUP BY category_name
HAVING avg_quantity > 21
ORDER BY row_count DESC;

-- Фільтр - чотири рядки з пропущеним першим рядком
SELECT
    categories.name AS category_name,
    COUNT(*) AS row_count,
    AVG(order_details.quantity) AS avg_quantity
FROM 
    order_details
INNER JOIN orders ON order_details.order_id = orders.id
INNER JOIN customers ON orders.customer_id = customers.id
INNER JOIN employees ON orders.employee_id = employees.employee_id
INNER JOIN shippers ON orders.shipper_id = shippers.id
INNER JOIN products ON order_details.product_id = products.id
INNER JOIN categories ON products.category_id = categories.id
INNER JOIN suppliers ON products.supplier_id = suppliers.id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10
GROUP BY category_name
HAVING avg_quantity > 21
ORDER BY row_count DESC
LIMIT 4 OFFSET 1;

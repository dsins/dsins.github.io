# PHP: MySQLi vs PDO - Secure CRUD Reference

**TL;DR:** Use PDO. MySQL-only projects can use MySQLi but gain nothing over PDO. New projects should skip MySQLi entirely.

---

## MySQLi vs PDO at a Glance

| | MySQLi | PDO |
|---|---|---|
| DB support | MySQL only | MySQL, PostgreSQL, SQLite, and more |
| Prepared statements | Supported, but verbose | Clean, consistent API |
| Named parameters (`:name`) | No | Yes |
| Transactions | Yes | Yes |
| Error handling | Manual / inconsistent | `ERRMODE_EXCEPTION` - throws on any error |
| Portability | Low | High |

---

## MySQLi - What to Avoid

```php
// UNSAFE - direct user input in query
$sql = "SELECT * FROM users WHERE name = '" . $_GET['name'] . "'";
$conn->query($sql);
// Input: John'; DROP TABLE users; -- → table deleted
```

MySQLi without prepared statements is a SQL injection vector. If you use MySQLi, you must use `prepare()` + `bind_param()` every time - which is more verbose than PDO with no added benefit.

---

## PDO - Connection

```php
$conn = new PDO("mysql:host=localhost;dbname=example_db", "username", "password");
$conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
// ERRMODE_EXCEPTION: throws PDOException on any error - no silent failures
```

---

## PDO CRUD

### INSERT

```php
$stmt = $conn->prepare("INSERT INTO users (name, age) VALUES (:name, :age)");
$stmt->bindValue(':name', 'John Doe', PDO::PARAM_STR);
$stmt->bindValue(':age', 25, PDO::PARAM_INT);
$stmt->execute();
```

### SELECT

```php
$stmt = $conn->query("SELECT * FROM users");
while ($row = $stmt->fetch(PDO::FETCH_ASSOC)) {
    echo $row['name'] . " - " . $row['age'];
}
```

### UPDATE

```php
$stmt = $conn->prepare("UPDATE users SET age = :age WHERE name = :name");
$stmt->bindValue(':age', 30, PDO::PARAM_INT);
$stmt->bindValue(':name', 'John Doe', PDO::PARAM_STR);
$stmt->execute();
```

### DELETE

```php
$stmt = $conn->prepare("DELETE FROM users WHERE name = :name");
$stmt->bindValue(':name', 'John Doe', PDO::PARAM_STR);
$stmt->execute();
```

---

## bindValue vs bindParam

```php
// bindValue - binds the VALUE at the time of the call
$stmt->bindValue(':name', $name, PDO::PARAM_STR);

// bindParam - binds the VARIABLE by reference (evaluated at execute time)
$stmt->bindParam(':name', $name, PDO::PARAM_STR);
// Use bindParam in loops where $name changes each iteration
```

### PDO Type Constants

| Constant | Use for |
|---|---|
| `PDO::PARAM_STR` | Strings |
| `PDO::PARAM_INT` | Integers |
| `PDO::PARAM_BOOL` | Booleans |
| `PDO::PARAM_NULL` | NULL values |

---

## Fetch Modes

```php
$stmt->fetch(PDO::FETCH_ASSOC);  // ['name' => 'John', 'age' => 25]
$stmt->fetch(PDO::FETCH_OBJ);    // $row->name, $row->age
$stmt->fetchAll(PDO::FETCH_ASSOC); // all rows as array
```

---

## Transactions

```php
try {
    $conn->beginTransaction();

    $conn->exec("INSERT INTO users (name, age) VALUES ('User1', 20)");
    $conn->exec("INSERT INTO users (name, age) VALUES ('User2', 25)");

    $conn->commit();
} catch (PDOException $e) {
    $conn->rollBack();
    echo "Transaction failed: " . $e->getMessage();
}
```

Use transactions whenever multiple queries must succeed or fail together (e.g., order + inventory deduction).

---

## Error Handling Pattern

```php
try {
    $conn = new PDO("mysql:host=localhost;dbname=example_db", "user", "pass");
    $conn->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    // ... queries

} catch (PDOException $e) {
    // Log $e->getMessage() - never echo raw DB errors to the user in production
    error_log($e->getMessage());
    echo "A database error occurred.";
} finally {
    $conn = null; // close connection
}
```

---

## Notes

- Always set `ATTR_ERRMODE` to `ERRMODE_EXCEPTION` - default silent failure mode hides bugs.
- Never interpolate `$_GET`/`$_POST` directly into SQL strings. Always use prepared statements.
- `PDO::FETCH_ASSOC` is preferred over default `FETCH_BOTH` (which returns duplicate columns by index and name).
- To switch databases (e.g., MySQL → PostgreSQL), only the DSN string changes - all prepared statement code stays the same.

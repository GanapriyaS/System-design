# 5. Database Isolation Levels

[← 4. Relational Databases](../basics/04-relational-databases.md) · [🏠 Index](../README.md) · [6. Database Scaling Techniques →](../basics/06-database-scaling-techniques.md)

---

Relational databases provide ACID guarantees, and **I** in ACID is "Isolation", and isolation levels help us tune them.

Isolation levels dictate **how much one transaction knows about the other**. (Transactions T1, T2 may run concurrently on CPU1, CPU2.) We look at each one of them and understand with examples.

### Repeatable Reads (mostly default SQL server)
Consistent reads within same transaction — even if other transaction committed 1st transaction would not see the changes (if value already read).

```
> describe users:
ID | Name
> select * from users:
1 | a

> SELECT @@transaction_ISOLATION
REPEATABLE-READ

> SET autocommit=0 (don't autocommit transaction)
```
-------------------------------------------------------------
<table style="width: 100%; border-collapse: collapse;">
  <style>
    td, th {
      border: 1px solid #ccc;
      padding: 8px;
      text-align: left;
      vertical-align: top;
    }
</style>
  <tr>
    <th>Transaction 1</th>
    <th>Transaction 2</th>
  </tr>
  <tr>
    <td><pre><code class="language-sql">&gt; START TRANSACTION:
</code></pre></td>
    <td><pre><code class="language-sql">&gt;  START TRANSACTION:
    </code></pre></td>
  </tr>
  <tr>
    <td><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a
</code></pre></td>
    <td><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a</code></pre></td>
  </tr>
    <tr>
    <td><pre><code class="language-sql">&gt; UPDATE users SET name='a-test' WHERE id=1; <br>

&gt; SELECT * FROM users WHERE id=1;
1, a-test
</code></pre></td>
    <td><br>REPEATABLE READ</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a</code></pre>
<br>READ COMMITTED</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a</code></pre>
<br>READ UNCOMMITTED</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a-test</code></pre>
<br>SERIALIZABLE</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
<b>Wait till commit</b></code></pre>
</td>

  </tr>

<tr>
    <td><pre><code class="language-sql">&gt; COMMIT;
</code></pre></td>
    

<td><br>REPEATABLE READ</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a</code></pre>
<br>READ COMMITTED</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a-test</code></pre>
<br>READ UNCOMMITTED</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a-test</code></pre>
<br>SERIALIZABLE</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a-test</code></pre>
</td>
  </tr>



<tr>
    <td><pre><code class="language-sql">&gt; ROLLBACK;
</code></pre></td>
    

<td><br>REPEATABLE READ</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a</code></pre>
<br>READ COMMITTED</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a</code></pre>
<br>READ UNCOMMITTED</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a</code></pre>
<br>SERIALIZABLE</br><pre><code class="language-sql">&gt; SELECT * FROM users WHERE id=1;
1, a</code></pre>
</td>
  </tr>
</table>


### Read Committed
Reads within the same transaction always read fresh value.
- **con:** multiple reads within same transaction are inconsistent

```
> SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITTED:

> SELECT @@transaction_ISOLATION
READ-COMMITED
```

### Read Uncommitted
Reads even uncommitted values from other txns — **"dirty read"**.

```
> SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED:

> SELECT @@transaction_ISOLATION
READ-UNCOMMITED
```

### Serializable
Every read is a **locking read** (depends on engine) and while one txn reads, others will have to wait.

```
> SET SESSION TRANSACTION ISOLATION LEVEL SERIALIZABLE:

> SELECT @@transaction_ISOLATION
SERIALIZABLE
```

> \* Storage engines can alter the implementation, so read documentation before you alter.

T1 ------------------
        ------------------- T2
T1 ---------
   ==CPU1==|=====CPU2======
---

[← 4. Relational Databases](../basics/04-relational-databases.md) · [🏠 Index](../README.md) · [6. Database Scaling Techniques →](../basics/06-database-scaling-techniques.md)

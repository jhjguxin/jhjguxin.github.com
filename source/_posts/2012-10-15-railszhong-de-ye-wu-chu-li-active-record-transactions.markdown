---
layout: post
title: "rails中的业务处理Active Record Transactions"
date: 2012-03-24 22:19
comments: true
categories: [rails,ruby]
tags: [rails,transation,业务，处理]
---
## rails中的业务处理Active Record Transactions
<a href="http://api.rubyonrails.org/classes/ActiveRecord/Transactions.html">Transactions</a> are protective blocks where SQL statements are only permanent if they can all succeed as one atomic action. The classic example is a transfer between two accounts where you can only have a deposit if the withdrawal succeeded and vice versa.

<a href="http://api.rubyonrails.org/classes/ActiveRecord/Transactions.html">Transactions</a>是保护行的代码块，用于只有你能够完成所有的元操作才permanent SQL statements（执行sql语句）。经典的例子是一个在两个帐号之间的业务，这里你只能在转账成功后才能有一个deposit存款，反之也是。

<a href="http://api.rubyonrails.org/classes/ActiveRecord/Transactions.html">Transactions</a> enforce the integrity of the database and guard the data against program errors or database break-downs. So basically you should use transaction blocks whenever you have a number of statements that must be executed together or not at all.

<a href="http://api.rubyonrails.org/classes/ActiveRecord/Transactions.html">Transactions</a> 保障了数据库的有效性，能够防止程序错误或者数据库故障对数据的影响。因此基本上你应该使用<a href="http://api.rubyonrails.org/classes/ActiveRecord/Transactions.html">Transactions</a>代码块无论何时你需要一系列的声明必须在一起执行或者什么都不做。

For example:
<pre data-second_best="[object Object]" data-result="[object Object]">ActiveRecord::Base.transaction do
  david.withdrawal(100)
  mary.deposit(100)
end</pre>
This example will only take money from David and give it to Mary if neither <code>withdrawal</code> nor <code>deposit</code> raise an exception. Exceptions will force a ROLLBACK that returns the database to the state before the transaction began. Be aware, though, that the objects will <em>not</em> have their instance data returned to their pre-transactional state.

这个例子仅仅从David那里转账一些钱给Mary如果汇款或者存款都没有异常抛出。意外情况发生则会强制一个回滚数据库到业务开始的状态。请保持清醒，这样，对象将不会有实例数据回到到它们的每一个业务来源地。
<h2 id="label-Different+Active+Record+classes+in+a+single+transaction">Different Active Record classes in a single transaction</h2>
Though the transaction class method is called on some Active Record class, the objects within the transaction block need not all be instances of that class. This is because transactions are per-database connection, not per-model.

即使transaction类方法被一些的Active Record class调用，在transaction代码块中的对象并不需要所有的实例都是来自那个类。这是因为transaction是基于每个数据连接的而不是每个model。

In this example a <code>balance</code> record is transactionally saved even though <code>transaction</code> is called on the <code>Account</code> class:

在这个例子中余额记录被transactionally保存即使<code>transaction也被Account类调用。</code>
<pre data-second_best="[object Object]" data-result="[object Object]">Account.transaction do
  balance.save!
  account.save!
end</pre>
The <code>transaction</code> method is also available as a model instance method. For example, you can also do this:

<code>transaction在model实例方法中也是可用的。例如你可以这样做：</code>
<pre data-second_best="[object Object]" data-result="[object Object]">balance.transaction do
  balance.save!
  account.save!
end</pre>
<h2 id="label-Transactions+are+not+distributed+across+database+connections"><a href="http://api.rubyonrails.org/classes/ActiveRecord/Transactions.html">Transactions</a> are not distributed across database connections</h2>
A transaction acts on a single database connection. If you have multiple class-specific databases, the transaction will not protect interaction among them. One workaround is to begin a transaction on each class whose models you alter:

一个transaction动作是一个单个的数据库连接。如果你的数据库指定了多个类，transaction将不会保护他们全部的相互影响。一个解决办法是开始一个transaction包含你要改变的model的每一个类：
<pre data-second_best="[object Object]" data-result="[object Object]">Student.transaction do
  Course.transaction do
    course.enroll(student)
    student.units += course.units
  end
end</pre>
This is a poor solution, but fully distributed transactions are beyond the scope of Active Record.

这是一个无赖的办法，但是完整的区域业务超出了Active Record的范围。
<h2 id="label-save+and+destroy+are+automatically+wrapped+in+a+transaction"><code>save</code> and <code>destroy</code> are automatically wrapped in a transaction</h2>
Both <code>save</code> and <code>destroy</code> come wrapped in a transaction that ensures that whatever you do in validations or callbacks will happen under its protected cover. So you can use validations to check for values that the transaction depends on or you can raise exceptions in the callbacks to rollback, including <code>after_*</code> callbacks.

保存和删除都是包装在一个业务中的确保无论你何时做验证或者回调都会在它的保护下。因此你可以使用验证来检查业务中的值在此基础上或者你可以在回调中抛出异常来回滚，包含<code>after_*</code> callbacks。

As a consequence changes to the database are not seen outside your connection until the operation is complete. For example, if you try to update the index of a search engine in <code>after_save</code> the indexer won’t see the updated record. The <code>after_commit</code> callback is the only one that is triggered once the update is committed. See below.

这样有一个后果就是在你的操作完成之前，数据库的改变都不会表现出来（不会突出于你的连接之外）。例如，如果你尝试使用<code>after_save</code>更新一个搜索引擎的索引，索引者将不会发现更新的记录。<code>after_commit回调是仅有的一个</code>它会在一旦更新完成就被触发。看下面。
<h2 id="label-Exception+handling+and+rolling+back">Exception handling and rolling back</h2>
<h2 id="label-Exception+handling+and+rolling+back">Exception handling and rolling back</h2>
Also have in mind that exceptions thrown within a transaction block will be propagated (after triggering the ROLLBACK), so you should be ready to catch those in your application code.

同样也要留心在一个业务代码块中的异常抛出将会被传播（在触发回调之后）。因此你应该准备好在应用程序中抓取这些异常。

One exception is the <code>ActiveRecord::Rollback</code> exception, which will trigger a ROLLBACK when raised, but not be re-raised by the transaction block.

<strong>Warning</strong>: one should not catch <code>ActiveRecord::StatementInvalid</code> exceptions inside a transaction block. <code>ActiveRecord::StatementInvalid</code> exceptions indicate that an error occurred at the database level, for example when a unique constraint is violated. On some database systems, such as PostgreSQL, database errors inside a transaction cause the entire transaction to become unusable until it's restarted from the beginning. Here is an example which demonstrates the problem:
<pre data-result="[object Object]"># Suppose that we have a Number model with a unique column called 'i'.
Number.transaction do
  Number.create(:i =&gt; 0)
  begin
    # This will raise a unique constraint error...
    Number.create(:i =&gt; 0)
  rescue ActiveRecord::StatementInvalid
    # ...which we ignore.
  end

  # On PostgreSQL, the transaction is now unusable. The following
  # statement will cause a PostgreSQL error, even though the unique
  # constraint is no longer violated:
  Number.create(:i =&gt; 1)
  # =&gt; "PGError: ERROR:  current transaction is aborted, commands
  #     ignored until end of transaction block"
end</pre>
One should restart the entire transaction if an <code>ActiveRecord::StatementInvalid</code> occurred.

应该重新开始业务如果<code>ActiveRecord::StatementInvalid发生了。</code>
<h2 id="label-Nested+transactions">Nested transactions</h2>
<code>transaction</code> calls can be nested. By default, this makes all database statements in the nested transaction block become part of the parent transaction. For example, the following behavior may be surprising:

<code>transaction可以嵌套调用。默认情况下，这将会将被嵌套的<code>transaction作为其中</code>所有的数据库声明</code>的父业务。
<pre data-result="[object Object]">User.transaction do
  User.create(:username =&gt; 'Kotori')
  User.transaction do
    User.create(:username =&gt; 'Nemu')
    raise ActiveRecord::Rollback
  end
end</pre>
creates both “Kotori” and “Nemu”. Reason is the <code>ActiveRecord::Rollback</code> exception in the nested block does not issue a ROLLBACK. Since these exceptions are captured in transaction blocks, the parent block does not see it and the real transaction is committed.

In order to get a ROLLBACK for the nested transaction you may ask for a real sub-transaction by passing <code>:requires_new =&gt; true</code>. <strong>If anything goes wrong, the database rolls back to the beginning of the sub-transaction without rolling back the parent transaction. 如果有任何错误，数据库将会回滚到子业务的开始状态，并没有回滚父业务。</strong>If we add it to the previous example:
<pre data-result="[object Object]">User.transaction do
  User.create(:username =&gt; 'Kotori')
  User.transaction(:requires_new =&gt; true) do
    User.create(:username =&gt; 'Nemu')
    raise ActiveRecord::Rollback
  end
end</pre>
<strong>only “Kotori” is created. (This works on MySQL and PostgreSQL, but not on SQLite3.)</strong>

<strong>Most databases don’t support true nested transactions.</strong> At the time of writing, the only database that we’re aware of that supports true nested transactions, is MS-SQL. Because of this, Active Record emulates nested transactions by using savepoints on MySQL and PostgreSQL. See <a href="http://dev.mysql.com/doc/refman/5.0/en/savepoint.html">dev.mysql.com/doc/refman/5.0/en/savepoint.html</a> for more information about savepoints.
<h3 id="label-Callbacks"><a href="http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html">Callbacks</a></h3>
There are two types of callbacks associated with committing and rolling back transactions: <code>after_commit</code> and <code>after_rollback</code>.

<code>after_commit</code> callbacks are called on every record saved or destroyed within a transaction immediately after the transaction is committed. <code>after_rollback</code> callbacks are called on every record saved or destroyed within a transaction immediately after the transaction or savepoint is rolled back.

These callbacks are useful for interacting with other systems since you will be guaranteed that the callback is only executed when the database is in a permanent state. For example, <code>after_commit</code> is a good spot to put in a hook to clearing a cache since clearing it from within a transaction could trigger the cache to be regenerated before the database is updated.
<h3 id="label-Caveats">Caveats</h3>
If you’re on MySQL, then do not use DDL operations in nested transactions blocks that are emulated with savepoints. That is, do not execute statements like ‘CREATE TABLE’ inside such blocks. This is because MySQL automatically releases all savepoints upon executing a DDL operation. When <code>transaction</code> is finished and tries to release the savepoint it created earlier, a database error will occur because the savepoint has already been automatically released. The following example demonstrates the problem:
<pre data-result="[object Object]">Model.connection.transaction do                           # BEGIN
  Model.connection.transaction(:requires_new =&gt; true) do  # CREATE SAVEPOINT active_record_1
    Model.connection.create_table(...)                    # active_record_1 now automatically released
  end                                                     # RELEASE savepoint active_record_1
                                                          # ^^^^ BOOM! database error!
end</pre>
Note that “TRUNCATE” is also a MySQL DDL statement!

The hexagonal architecture divides the system into loosely-coupled interchangeable components, such as application core, user interface, data repositories, test scripts and other system interfaces etc.

![[Pasted image 20240324145429.png]]

==This approach helps us to achieve to make the business (domain) layer independent from framework, UI , database or any other external components.==

This also gives **flexibility** to make changes on the adapters easily. For example you can swap out Oracle or SQL Server, for Mongo or something else. Your business rules are not bound to the database.

This pattern allows to isolate the core logic of application from outside concerns. Having the core logic isolated means you can easily change data source details **without a significant impact or major code rewrites to the codebase**.

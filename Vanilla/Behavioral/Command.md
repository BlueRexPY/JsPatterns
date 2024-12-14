# Command Pattern

- #Behavioral

- Decoupling state from logic.

- Solves the problem of decoupling the sender and receiver of a request, allowing for more flexible and extensible code.

## Example - No-SQL Migration Manger

```javascript
// Command Interface
class MigrationCommand {
  constructor(name) {
    this.name = name;
    this.executedAt = null;
  }

  async execute() {
    throw new Error("Subclasses must implement execute method");
  }

  async rollback() {
    throw new Error("Subclasses must implement rollback method");
  }
}

// Concrete Migration Commands
class CreateUserIndexMigration extends MigrationCommand {
  constructor() {
    super("create_user_index_migration");
  }

  async execute() {
    const UserModel = mongoose.model("User");

    await UserModel.createIndexes([
      { key: { email: 1 }, unique: true },
      { key: { createdAt: -1 } },
    ]);
  }

  async rollback() {
    const UserModel = mongoose.model("User");
    await UserModel.collection.dropIndexes();
  }
}

class AddUserRoleMigration extends MigrationCommand {
  constructor() {
    super("add_user_role_migration");
  }

  async execute() {
    const UserModel = mongoose.model("User");
    await UserModel.updateMany(
      { role: { $exists: false } },
      { $set: { role: "user" } }
    );
  }

  async rollback() {
    const UserModel = mongoose.model("User");
    await UserModel.updateMany({ role: "user" }, { $unset: { role: 1 } });
  }
}

// Invoker
class MigrationManager {
  constructor() {
    this.migrations = [];
    this.executedMigrations = [];
  }

  register(migrationCommand) {
    this.migrations.push(migrationCommand);
    return this;
  }

  async runMigrations() {
    for (const migration of this.migrations) {
      try {
        await migration.execute();
        migration.executedAt = new Date();
        this.executedMigrations.push(migration);
      } catch (error) {
        console.error(`Migration ${migration.name} failed:`, error);

        await this.rollbackMigrations();
        throw error;
      }
    }
  }

  async rollbackMigrations() {
    for (const migration of this.executedMigrations.reverse()) {
      try {
        await migration.rollback();
      } catch (error) {
        console.error(`Rollback for ${migration.name} failed:`, error);
      }
    }
  }
}

// Usage
async function runDatabaseMigrations() {
  await mongoose.connect("mongodb://localhost:27017/myapp", {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  });

  // Define Mongoose models
  mongoose.model(
    "User",
    new mongoose.Schema({
      email: String,
      createdAt: { type: Date, default: Date.now },
      role: String,
    })
  );

  // Create Migration Manager
  const migrationManager = new MigrationManager();

  // Register migrations
  migrationManager
    .register(new CreateUserIndexMigration())
    .register(new AddUserRoleMigration());

  // Run migrations
  try {
    await migrationManager.runMigrations();
    console.log("All migrations completed successfully");
  } catch (error) {
    console.error("Migration process failed:", error);
  } finally {
    await mongoose.connection.close();
  }
}
```

## Rating

- #DogAssFart: Implementation via classes in most scenarios is an overhead, you can use objects with callbacks for commands and [closure](/Vanilla/Behavioral/Closure.md) for state as:

  ```javascript
  const migrationManager = () => {
    const migrations = [];
    const executedMigrations = [];

    const register = (migration) => migrations.push(migration);

    const runMigrations = () => migrations.forEach();

    const rollbackMigrations = () => executedMigrations.forEach();

    return {
      register,
      runMigrations,
      rollbackMigrations,
    };
  };

  const createUserIndexMigration = {
    execute: () => void 0,
    rollback: () => void 0,
  };
  ```

## Additional Resources

- [Refactoring Guru](https://refactoring.guru/design-patterns/command)
- [patterns.dev](https://www.patterns.dev/vanilla/command-pattern)
- [oodesign](https://www.oodesign.com/command-pattern)

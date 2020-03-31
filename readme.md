# [Workshop] **An introduction to GraphQL APIs for Frontend Developers**

## Introduction

GraphQL seems shiny on the frontend, and frontend developers love it because of the flexibility to pick and choose the right size of data for our UI. Where the developer experience gets ugly is when you try to build the backend that supports a GraphQL API.

*See* *slides* *for detailed intro.*




## Exercise 1: Setup Frontend

**Task 1: Clone Starter Code**

```bash
git clone https://github.com/christiannwamba/graphql-api-for-frontend-dev-workshop-starter
```

**Task 2: Install npm depencies**

```bash
# Move into the project folder
cd graphql-api-for-frontend-dev-workshop-starter

# Install dependencies
npm install
```

**Task 3: Start app**

```bash
npm start
```

**Task 4: Code Review**

Open `src/App.js`. 

- You should see an import from `initialData.js`

```js
import initialData from './initialData';
```


- It's test data that looks like:

```js
const initialData = [
  {
    type: "Mammal",
    img: "....../view-of-elephant-in-water-247431.jpg",
    animals: [
      {
        name: "Dog"
      },
    ]
  },
  {
    type: "Bird",
    img: ".../shallow-focus-photography-of-green-yellow-and-blue-bird-2190209.jpg",
    animals: [
      {
        name: "Chicken"
      },
      {
        name: "Duck"
      }
    ]
  },
  {
    type: "Reptile",
    img: ".../photo-of-a-snake-3280908.jpg",
    animals: [
      {
        name: "Snake"
      }
    ]
  }
];
```

Each class of animals has animals children in an array.


- You should also see how we are rendering the data:

```js
<main>
  {classes.map(classData => (
    <Flip
      key={classData.type}
      flip={() => {
        //...
      }}
      Front={() => (
        <div
          style={{ backgroundImage: `url(${classData.img})`, height: "100%" }}
        >
          <h2 className="title">{classData.type}</h2>
        </div>
      )}
      Back={() => (
        <div className="List">
          {classData.animals.map(animal => (
            <div className="List__item" key={animal.name}>
              {animal.name}
            </div>
          ))}
        </div>
      )}
      flipped={classData.flipped}
    />
  ))}
</main>    
```

- The front of the card shows the class while the back of the card shows the list of animals that belongs to a given class.



## Exercise 2: Setup Backend

**Task 1: Create a Docker Compose File**

```bash
touch docker-compose.yml
```

```bash
version: '3.6'
services:
  postgres:
    image: postgres:12
    restart: always
    volumes:
    - db_data:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: postgrespassword
  graphql-engine:
    image: hasura/graphql-engine:v1.1.0
    ports:
    - "4400:8080"
    depends_on:
    - "postgres"
    restart: always
    environment:
      HASURA_GRAPHQL_DATABASE_URL: postgres://postgres:postgrespassword@postgres:5432/postgres
      HASURA_GRAPHQL_ENABLE_CONSOLE: "true"
volumes:
  db_data:
```

**Task 2: Start Backend**

```bash
docker-compose up -d
```

Go to port `4400` to see your backend live

![](https://paper-attachments.dropbox.com/s_53154D4B07E8AB94D808ADDE69097613EA614C7313B62779DB91DFA6891FC291_1584341245257_image.png)


You also have a GraphQL API live at `http://localhost:4400/v1/graphql`.



## Exercise 3: Add Tables

**Task 1: Create Table to Store Animal Classes**


- *Create Table Page*: Go to the *create table page* by clicking on **Data** from the Navbar and click the **Create Table** button.


![](https://paper-attachments.dropbox.com/s_53154D4B07E8AB94D808ADDE69097613EA614C7313B62779DB91DFA6891FC291_1584342048257_image.png)

- *Configure Table*: Create a `class` table with just two fields: `id` and `type`. Then select `id` to be the primary key:


![](https://paper-attachments.dropbox.com/s_53154D4B07E8AB94D808ADDE69097613EA614C7313B62779DB91DFA6891FC291_1584348028758_image.png)

- *Save Table*: Click the **Add Table** button at the bottom of the page.

**Task 2: Test Query and Mutation on Class Table:**

Run the following in the query window to query the class table:

```gql
query MyQuery {
  class {
    type
  }
}
```

![](https://paper-attachments.dropbox.com/s_53154D4B07E8AB94D808ADDE69097613EA614C7313B62779DB91DFA6891FC291_1584348215344_image.png)


Run the following in the query window to add an item to the class table:

```gql
mutation MyMutation {
  insert_class(objects: {type: "Mammal", id: 1}) {
    affected_rows
  }
}
```

**Task 3: Create Tables using SQL Commands**

If writing raw SQL is your jam, you can also use it to create tables. Head to the SQL query window:


![](https://paper-attachments.dropbox.com/s_53154D4B07E8AB94D808ADDE69097613EA614C7313B62779DB91DFA6891FC291_1584348871695_image.png)

```sql
CREATE TABLE IF NOT EXISTS public."animal" (
  "name" TEXT,
  "class_id" INT,
  "id" SERIAL PRIMARY KEY
);
```

## Exercise 3: Create Relationships

**Task 1: Add a Table Relationship**

Go to the **Data** page, select **animal** from the sidebar and click the **Modify** tab:


![](https://paper-attachments.dropbox.com/s_53154D4B07E8AB94D808ADDE69097613EA614C7313B62779DB91DFA6891FC291_1584349767800_image.png)


Scroll down to **Foreign Keys** and click **Add**. Complete the foreign key form as shown in the screenshot the click **Save**:


![](https://paper-attachments.dropbox.com/s_53154D4B07E8AB94D808ADDE69097613EA614C7313B62779DB91DFA6891FC291_1584349893514_image.png)


**Task 2: Add an Object Relationship**

Go to each of tables and click the Relationships tab. You should see a suggestion to add an object relationship — add it. This suggestion is based on the fact that we already related both tables with a foreign key.

![](https://paper-attachments.dropbox.com/s_53154D4B07E8AB94D808ADDE69097613EA614C7313B62779DB91DFA6891FC291_1584350707976_image.png)

![](https://paper-attachments.dropbox.com/s_53154D4B07E8AB94D808ADDE69097613EA614C7313B62779DB91DFA6891FC291_1584350754820_image.png)

## Exercise 4: Seed the Database

You can seed your database using SQL commands.

**Task 1: Seed Class**

```sql
INSERT INTO public."class" ("id", "type", "img") VALUES
    (1,'Mammal','https://res.cloudinary.com/codebeast/image/upload/c_fill,h_400,q_50,w_400/v1584867791/Animal%20Classes/view-of-elephant-in-water-247431.jpg'),
    (2,'Bird','https://res.cloudinary.com/codebeast/image/upload/c_fill,h_400,q_50,w_400/v1584867879/Animal%20Classes/shallow-focus-photography-of-green-yellow-and-blue-bird-2190209.jpg'),
    (3,'Reptile','https://res.cloudinary.com/codebeast/image/upload/c_fill,h_400,q_50,w_400/v1584868136/Animal%20Classes/photo-of-a-snake-3280908.jpg'),
    (4,'Fish','https://res.cloudinary.com/codebeast/image/upload/c_fill,h_400,q_50,w_400/v1584868066/Animal%20Classes/shark-underwater-2747248.jpg'),
    (5,'Amphibian','https://res.cloudinary.com/codebeast/image/upload/c_fill,h_400,q_50,w_400/v1584868107/Animal%20Classes/close-up-photo-of-green-frog-3656636.jpg'),
    (6,'Bug','https://res.cloudinary.com/codebeast/image/upload/c_fill,h_400,q_50,w_400/v1584868169/Animal%20Classes/black-and-white-butterfly-on-red-flower-53957.jpg'),
    (7,'Invertebrate','https://res.cloudinary.com/codebeast/image/upload/c_fill,h_400,q_50,w_400/v1584868228/Animal%20Classes/three-jellyfishes-2730355.jpg');
```

**Task 2: Seed Animal**

```sql
INSERT INTO public."animal" ("name", "class_id")  VALUES
    ('aardvark',1),
    ('antelope',1),
    ('bass',4),
    ('bear',1),
    ('boar',1),
    ('buffalo',1),
    ('calf',1),
    ('carp',4),
    ('catfish',4),
    ('cavy',1),
    ('cheetah',1),
    -- Get full list from gist https://gist.github.com/christiannwamba/f6f1aa1b87c455c88764b749ad24d458
```

## Exercise 5: Test Queries

```gql
# Get classess with their animals
query MyQuery {
  class {
    animals {
      name
    }
  }
}

# Get animals with the class they belong to
query MyQuery {
  animal {
    name
    class {
      type
    }
  }
}

# Get annimals that are mammals
query MyQuery {
  animal(where: {class: {type: {_eq: "Mammal"}}}) {
    name
  }
}
```


## Exercise 5: Setup Apollo

**Task 1: Install Apollo Dependencies**

```bash
npm install apollo-boost @apollo/react-hooks graphqlnpm install apollo-boost @apollo/react-hooks graphql
```

**Task 2: Create Apollo Client**

Create Apollo client in `src/index.js`:

```js
import ApolloClient from 'apollo-boost';

const client = new ApolloClient({
  uri: '<HASURA GRAPHQL API>',
});
```

**Task 3: Connect Client to App with ApolloProvider**

```js
ReactDOM.render(
  <React.StrictMode>
    <ApolloProvider client={client}>
      <App />
    </ApolloProvider>
  </React.StrictMode>,
  rootElement
);
```

## Exercise 6: Query Class

**Task 1: Import Apollo Tools**

Import Apollo query tools in `src/App.js`:

```js
import gql from 'graphql-tag';
import { useQuery } from '@apollo/react-hooks';
```

**Task 2: Create Query**

Create a query and store it in a constant:

```js
const CLASSES = gql`
  query MyQuery {
    class {
      id
      type
      img
      animals {
        id
        name
      }
    }
  }
`;
```

Query in your component:

```js
const { loading, error, data } = useQuery(CLASSES);
```

**Task 3: Add error and loading logic:**

```js
if(loading) {
  return <div>Loading...</div>
}

if(error) {
  return <div>Something went wrong!</div>
}
```

**Task 4: Display Data:**

Replace `initialData.map` with `data.class.map`


## Bonus: Extend a GraphQL API


- [Create a serverless function](http://github.com/christiannwamba/affordable-serverless) 
- [Create a derived action](https://hasura.io/docs/1.0/graphql/manual/actions/derive.html)
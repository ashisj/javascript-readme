## Container / presentational pattern in react

The contianer/presentational pattern in React is most suited for the class-based component where we cannot use the hooks.
The hook/view pattern is for the functional components in React.

```js

const useBeer = () => {
  const [beers, setBeers] = useState([]);
  useEffect(() => {
    fetch("https://api.punkapi.com/v2/beers?page=1&per_page=10")
      .then((res) => res.json())
      .then((res) => setBeers(res));
  }, []);

  return beers;
};

const BeerContainer = () => {
  const [beers, setBeers] = useState([]);

    return <h1>Beer</h1>;
}
```

## Higher order component pattern

A higher-order component is a pattern that can be created with the React components. We can pass the reusable logic as props to the functional components to implement abstraction, reusability, and uniformity in the code.

To create an HOC we follow a naming convention starting the function name with `with` keyword like withFetchData().

```js
export default ProductList = ({ data }) => {
  return data.map((e) => <div>{e.title}</div>);
};


const { useState, useEffect } = require("react");

export default withFetchData = (Element, link) => {
  return (props) => {
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState("");
    const [data, setData] = useState([]);

    useEffect(() => {
      const fetchData = async () => {
        try {
          setLoading(true);
          let response = await fetch(link);
          response = await response.json();
          console.log("ashis", response);

          setData(response);
        } catch (e) {
          setError(e.message);
        } finally {
          setLoading(false);
        }
      };

      fetchData();
    }, []);

    if (loading) {
      return <>Loading... </>;
    }
    if (error) return <h4>{error}</h4>;

    return <Element data={data} {...props} />;
  };
};


// products.jsx
const Todos = withFetchData(
  ProductsList,
  "https://jsonplaceholder.typicode.com/todos"
);

const Photos = withFetchData(
  ProductsList,
  "https://jsonplaceholder.typicode.com/photos"
);

const Albums = withFetchData(
  ProductsList,
  "https://jsonplaceholder.typicode.com/albums"
);

export {
  Todos,
  Photos,
  Albums
};

import {Todos, Photos, Albums} from 'products'

export default function App() {
  return (
    <div className="App">
      <Todos />
      <Photos />
      <Albums />
    </div>
  );
};
```

## Provide Data passing pattern

We can avoid the props drill down by implementing the provider design pattern in ReactJS with the help of ContextAPI

```js
const FeatureFlag = React.createContext({});

export const FeaturFlagProvider = ({ children }) => {
  const [features, setFeatures] = useState({
    darakMode: true,
    chatEnabled: false,
  });

  // to update features
  useEffect(() => {}, []);

  return (
    <FeatureFlag.Provider value={{ features }}>{children}</FeatureFlag.Provider>
  );
};

const ChatWrapper = () => {
  const { features } = React.useContext(FeatureFlag);
  return featurs.darkMode ? "Dark Mode" : "Light Mode";
};

const App = () => {
  return (
    <>
      <FeaturFlagProvider>
        <ChatWrapper />
      </FeaturFlagProvider>
    </>
  );
};

```

## Incremental static re-rendering vs Server-side generated
## Client side rendering and Server Side rendering
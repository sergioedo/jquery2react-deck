---
theme: default
title: Aprendiendo React
info: |
  Migración inicial desde mdx-deck + CodeSurfer a Slidev.
  Presentación: Aprendiendo React - Desaprendiendo JQuery.
colorSchema: dark
lineNumbers: true
transition: slide-left
drawings:
  persist: false
mdc: true
---

# Aprendiendo ⚛️React

### Desaprendiendo 💲JQuery

-- Reactionarios de https://www.webreactiva.com/ --

---

# Objetivo de la sesión

<div class="text-left text-2xl mt-10">

- Pasar de una lógica imperativa estilo JQuery
- Interpretar la UI como una función del estado
- Entender estados, efectos, cálculos y render
- Refactorizar hacia custom hooks y componentes

</div>

---

# 1. Selección de Personajes

<iframe
  class="w-4/5 h-100 rounded shadow-lg border border-gray-700"
  src="https://jquery2react-4c55pokc5-sergioedo.vercel.app/"
/>

---

# Versión estática, no reactiva

```js
import "./App.css";

const App = () => {
  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>
      <div>
        <label>Elige un personaje:</label>
        <select id="character">
          <option value="13.5">Jon Snow (13.5€)</option>
          <option value="16.5">
            Danerys Targaryen (16.5€)
          </option>
          <option value="19">Tyrion Lannister (19€)</option>
        </select>
      </div>
      <div id="result">
        Precio total: <span id="total">13.50€</span>
      </div>
    </>
  );
};
```

---

# JSX es casi puro HTML

```js {6-19}
import "./App.css";

const App = () => {
  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>
      <div>
        <label>Elige un personaje:</label>
        <select id="character">
          <option value="13.5">Jon Snow (13.5€)</option>
          <option value="16.5">
            Danerys Targaryen (16.5€)
          </option>
          <option value="19">Tyrion Lannister (19€)</option>
        </select>
      </div>
      <div id="result">
        Precio total: <span id="total">13.50€</span>
      </div>
    </>
  );
};
```

---

# Añadimos estado + evento con `setState`

```js {1|5|13-18|28-31}
import { useState } from "react";
import "./App.css";

const App = () => {
  const [totalValue, setTotalValue] = useState(13.5);

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>

      <div>
        <label>Elige un personaje:</label>
        <select
          id="character"
          onChange={(e) =>
            setTotalValue(Number(e.target.value))
          }
        >
          <option value="13.5">Jon Snow (13.5€)</option>
          <option value="16.5">
            Danerys Targaryen (16.5€)
          </option>
          <option value="19">Tyrion Lannister (19€)</option>
        </select>
      </div>

      <div id="result">
        Precio total:{" "}
        <span id="total">{totalValue.toFixed(2)}€</span>
      </div>
    </>
  );
};
```

---

# Clave mental

<div class="text-left text-2xl mt-10">

En React no actualizamos directamente el DOM.

<br />

Actualizamos el estado y React vuelve a pintar.

</div>

<div class="text-left text-2xl mt-8">

- <span v-click>Estado</span>
- <span v-click>Evento</span>
- <span v-click>Nuevo estado</span>
- <span v-click>Nuevo render</span>

</div>

---

# 2. Opción de dragón extra

<iframe
  class="w-4/5 h-100 rounded shadow-lg border border-gray-700"
  src="https://jquery2react-4gn66b207-sergioedo.vercel.app/"
/>

---

# Personajes escritos directamente en JSX

```js
import { useState } from "react";
import "./App.css";

const App = () => {
  const [totalValue, setTotalValue] = useState(13.5);

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>
      <div>
        <label>Elige un personaje:</label>
        <select
          id="character"
          onChange={(e) =>
            setTotalValue(Number(e.target.value))
          }
        >
          <option value="13.5">Jon Snow (13.5€)</option>
          <option value="16.5">
            Danerys Targaryen (16.5€)
          </option>
          <option value="19">Tyrion Lannister (19€)</option>
        </select>
      </div>
      <div>
        Precio total: <span>{totalValue.toFixed(2)}€</span>
      </div>
    </>
  );
};
```

---

# Personajes `data driven`

```js {4-8|23-35}
import { useState } from "react";
import "./App.css";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  { name: "Danerys Targaryen", price: 16.5 },
  { name: "Tyrion Lannister", price: 19 },
];

const App = () => {
  const [totalValue, setTotalValue] = useState(13.5);

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>
      <div>
        <label>Elige un personaje:</label>
        <select
          onChange={(e) =>
            setTotalValue(Number(e.target.value))
          }
        >
          {characters.map((character) => {
            return (
              <option
                key={character.name}
                value={character.price}
              >
                {`${character.name} (${character.price}€)`}
              </option>
            );
          })}
        </select>
      </div>
      <div>
        Precio total: <span>{totalValue.toFixed(2)}€</span>
      </div>
    </>
  );
};
```

---

# Índice de personaje como estado

```js {11-14|15-16|27-35}
import { useState } from "react";
import "./App.css";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  { name: "Danerys Targaryen", price: 16.5 },
  { name: "Tyrion Lannister", price: 19 },
];

const App = () => {
  const [
    selectedCharacterIndex,
    setSelectedCharacterIndex,
  ] = useState(0);

  const totalValue =
    characters[selectedCharacterIndex].price;

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>

      <div>
        <label>Elige un personaje:</label>
        <select
          onChange={(e) =>
            setSelectedCharacterIndex(
              Number(e.target.value)
            )
          }
        >
          {characters.map((character, index) => {
            return (
              <option
                key={character.name}
                value={index}
              >
                {`${character.name} (${character.price}€)`}
              </option>
            );
          })}
        </select>
      </div>

      <div>
        Precio total: <span>{totalValue.toFixed(2)}€</span>
      </div>
    </>
  );
};
```

---

# Incluir dragón: estado + cálculo

```js {9|18-23|25-27}
import { useState } from "react";
import "./App.css";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  { name: "Danerys Targaryen", price: 16.5 },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const [
    selectedCharacterIndex,
    setSelectedCharacterIndex,
  ] = useState(0);

  const selectedCharacter =
    characters[selectedCharacterIndex];

  const showDragonOption = selectedCharacterIndex === 1;
  const [includeDragon, setIncludeDragon] = useState(false);

  const totalValue =
    selectedCharacter.price +
    (showDragonOption && includeDragon ? DRAGON_PRICE : 0);

  return null;
};
```

---

# Incluir dragón: mostrar con CSS

```js {1-17}
<div
  style={{
    display: showDragonOption ? "block" : "none",
  }}
>
  <input
    type="checkbox"
    onChange={(e) =>
      setIncludeDragon(e.target.checked)
    }
  />
  <label htmlFor="dragon">
    Con dragón en el hombro ({DRAGON_PRICE}€ más)
  </label>
</div>
```

---

# Evitar renderizar → sincronizar estado

```js {1-15}
{showDragonOption && (
  <div>
    <input
      type="checkbox"
      checked={includeDragon}
      onChange={(e) =>
        setIncludeDragon(e.target.checked)
      }
    />
    <label htmlFor="dragon">
      Con dragón en el hombro ({DRAGON_PRICE}€ más)
    </label>
  </div>
)}
```

---

# Código completo con dragón

```js {21-22|24-27|49-63}
import { useState } from "react";
import "./App.css";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  { name: "Danerys Targaryen", price: 16.5 },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const [
    selectedCharacterIndex,
    setSelectedCharacterIndex,
  ] = useState(0);

  const selectedCharacter =
    characters[selectedCharacterIndex];

  const showDragonOption = selectedCharacterIndex === 1;
  const [includeDragon, setIncludeDragon] = useState(false);

  const totalValue =
    selectedCharacter.price +
    (showDragonOption && includeDragon ? DRAGON_PRICE : 0);

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>

      <div>
        <label>Elige un personaje:</label>
        <select
          onChange={(e) =>
            setSelectedCharacterIndex(
              Number(e.target.value)
            )
          }
        >
          {characters.map((character, index) => {
            return (
              <option
                key={character.name}
                value={index}
              >
                {`${character.name} (${character.price}€)`}
              </option>
            );
          })}
        </select>
      </div>

      {showDragonOption && (
        <div>
          <input
            type="checkbox"
            checked={includeDragon}
            onChange={(e) =>
              setIncludeDragon(e.target.checked)
            }
          />
          <label htmlFor="dragon">
            Con dragón en el hombro ({DRAGON_PRICE}€ más)
          </label>
        </div>
      )}

      <div>
        Precio total: <span>{totalValue.toFixed(2)}€</span>
      </div>
    </>
  );
};
```

---

# 3. Cantidad de producto

<iframe
  class="w-4/5 h-100 rounded shadow-lg border border-gray-700"
  src="https://jquery2react-i5oz3trtk-sergioedo.vercel.app/"
/>

---

# Estado + cálculo de cantidad

```js {22-29}
const [quantity, setQuantity] = useState(1);

const totalValue =
  (selectedCharacter.price +
    (showDragonOption && includeDragon
      ? DRAGON_PRICE
      : 0)) *
  quantity;
```

---

# Componente + valor-estado + evento-setState

```js {1-11}
<div>
  <label htmlFor="quantity">Cantidad:</label>
  <input
    type="number"
    min="1"
    value={quantity}
    onChange={(e) =>
      setQuantity(Number(e.target.value))
    }
  />
</div>
```

---

# Código completo con cantidad

```js {23|25-31|72-82}
import { useState } from "react";
import "./App.css";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  { name: "Danerys Targaryen", price: 16.5 },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const [
    selectedCharacterIndex,
    setSelectedCharacterIndex,
  ] = useState(0);

  const selectedCharacter =
    characters[selectedCharacterIndex];

  const showDragonOption = selectedCharacterIndex === 1;
  const [includeDragon, setIncludeDragon] = useState(false);

  const [quantity, setQuantity] = useState(1);

  const totalValue =
    (selectedCharacter.price +
      (showDragonOption && includeDragon
        ? DRAGON_PRICE
        : 0)) *
    quantity;

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>

      <div>
        <label>Elige un personaje:</label>
        <select
          onChange={(e) =>
            setSelectedCharacterIndex(
              Number(e.target.value)
            )
          }
        >
          {characters.map((character, index) => {
            return (
              <option
                key={character.name}
                value={index}
              >
                {`${character.name} (${character.price}€)`}
              </option>
            );
          })}
        </select>
      </div>

      {showDragonOption && (
        <div>
          <input
            type="checkbox"
            checked={includeDragon}
            onChange={(e) =>
              setIncludeDragon(e.target.checked)
            }
          />
          <label htmlFor="dragon">
            Con dragón en el hombro ({DRAGON_PRICE}€ más)
          </label>
        </div>
      )}

      <div>
        <label htmlFor="quantity">Cantidad:</label>
        <input
          type="number"
          min="1"
          value={quantity}
          onChange={(e) =>
            setQuantity(Number(e.target.value))
          }
        />
      </div>

      <div>
        Precio total: <span>{totalValue.toFixed(2)}€</span>
      </div>
    </>
  );
};
```

---

# 4. Cálculo de impuestos

<iframe
  class="w-4/5 h-100 rounded shadow-lg border border-gray-700"
  src="https://jquery2react-e49obbdu6-sergioedo.vercel.app/"
/>

---

# Función random country + valor por defecto

```js {3-13|15-18}
import { useState, useEffect } from "react";
import "./App.css";

const getRandomCountry = async () => {
  const response = await fetch(
    "https://random-data-api.com/api/v2/addresses"
  );

  const country = await response.json();

  return {
    name: country.country,
    code: country.country_code,
  };
};

const DEFAULT_COUNTRY = {
  name: "Spain",
  code: "ES",
};
```

---

# Estado país + fetch inicial con `useEffect`

```js {1|3-10}
const [country, setCountry] = useState(DEFAULT_COUNTRY);

useEffect(() => {
  const setInitialCountry = async () => {
    const country = await getRandomCountry();
    setCountry(country);
  };

  setInitialCountry();
}, []);
```

---

# Cálculo de impuestos

```js {1-4|6-13|15-16}
const extraDragon =
  showDragonOption && includeDragon ? DRAGON_PRICE : 0;

const totalValue =
  (selectedCharacter.price + extraDragon) * quantity;

const countryLetter = country.name
  .charAt(0)
  .toLowerCase();

const taxPercentage = ["a", "e", "i", "o", "u"].includes(
  countryLetter
)
  ? 10
  : 20;

const totalValueWithTaxes =
  totalValue + totalValue * (taxPercentage / 100);
```

---

# Mostramos impuestos + nuevo total

```jsx {1-14|16-21}
<div>
  Impuesto: <span>{taxPercentage}%</span>
  <span>
    {country?.code && (
      {`https://flagsapi.com/${country.code}/flat/64.png`}
    )}
  </span>
</div>

<div>
  Precio total:{" "}
  <span>{totalValueWithTaxes.toFixed(2)}€</span>
</div>
```

---

# Código completo con impuestos

```js {3-18|39-48|54-63|115-125|129}
import { useState, useEffect } from "react";
import "./App.css";

const getRandomCountry = async () => {
  const response = await fetch(
    "https://random-data-api.com/api/v2/addresses"
  );

  const country = await response.json();

  return {
    name: country.country,
    code: country.country_code,
  };
};

const DEFAULT_COUNTRY = {
  name: "Spain",
  code: "ES",
};

const characters = [
  { name: "Jon Snow", price: 13.5 },
  { name: "Danerys Targaryen", price: 16.5 },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const [
    selectedCharacterIndex,
    setSelectedCharacterIndex,
  ] = useState(0);

  const selectedCharacter =
    characters[selectedCharacterIndex];

  const showDragonOption = selectedCharacterIndex === 1;
  const [includeDragon, setIncludeDragon] = useState(false);

  const [quantity, setQuantity] = useState(1);

  const [country, setCountry] = useState(DEFAULT_COUNTRY);

  useEffect(() => {
    const setInitialCountry = async () => {
      const country = await getRandomCountry();
      setCountry(country);
    };

    setInitialCountry();
  }, []);

  const extraDragon =
    showDragonOption && includeDragon ? DRAGON_PRICE : 0;

  const totalValue =
    (selectedCharacter.price + extraDragon) * quantity;

  const countryLetter = country.name
    .charAt(0)
    .toLowerCase();

  const taxPercentage = ["a", "e", "i", "o", "u"].includes(
    countryLetter
  )
    ? 10
    : 20;

  const totalValueWithTaxes =
    totalValue + totalValue * (taxPercentage / 100);

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>

      <div>
        <label>Elige un personaje:</label>
        <select
          onChange={(e) =>
            setSelectedCharacterIndex(
              Number(e.target.value)
            )
          }
        >
          {characters.map((character, index) => {
            return (
              <option
                key={character.name}
                value={index}
              >
                {`${character.name} (${character.price}€)`}
              </option>
            );
          })}
        </select>
      </div>

      {showDragonOption && (
        <div>
          <input
            type="checkbox"
            checked={includeDragon}
            onChange={(e) =>
              setIncludeDragon(e.target.checked)
            }
          />
          <label htmlFor="dragon">
            Con dragón en el hombro ({DRAGON_PRICE}€ más)
          </label>
        </div>
      )}

      <div>
        <label htmlFor="quantity">Cantidad:</label>
        <input
          type="number"
          min="1"
          value={quantity}
          onChange={(e) =>
            setQuantity(Number(e.target.value))
          }
        />
      </div>

      <div>
        Impuesto: <span>{taxPercentage}%</span>
        <span>
          {country?.code && (
            {`https://flagsapi.com/${country.code}/flat/64.png`}
          )}
        </span>
      </div>

      <div>
        Precio total:{" "}
        <span>{totalValueWithTaxes.toFixed(2)}€</span>
      </div>
    </>
  );
};
```

---

# Patrón de ⚛️React⚛️

<div class="text-left text-3xl mt-12">

- Estados (`useState`)
- Efectos (`useEffect`)
- Cálculos JavaScript
- Render JSX data driven

</div>

---

# Refactor con ⚛️React⚛️

<div class="text-left text-2xl mt-12">

- Estados + efectos ⇒ <ins>**Custom Hook**</ins>  
  (_useMiCosaDeNegocio()_)

- Render ⇒ <ins>**Componentes**</ins>  
  (_`<MiCosaDeNegocio />`_)

</div>

---

# Hook `useRandomCountry`

```js {38-48}
import { useState, useEffect } from "react";
import "./App.css";

const getRandomCountry = async () => {
  const response = await fetch(
    "https://random-data-api.com/api/v2/addresses"
  );

  const country = await response.json();

  return {
    name: country.country,
    code: country.country_code,
  };
};

const DEFAULT_COUNTRY = {
  name: "Spain",
  code: "ES",
};

const characters = [
  { name: "Jon Snow", price: 13.5 },
  { name: "Danerys Targaryen", price: 16.5 },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const [
    selectedCharacterIndex,
    setSelectedCharacterIndex,
  ] = useState(0);

  const selectedCharacter =
    characters[selectedCharacterIndex];

  const showDragonOption = selectedCharacterIndex === 1;
  const [includeDragon, setIncludeDragon] = useState(false);

  const [quantity, setQuantity] = useState(1);

  const [country, setCountry] = useState(DEFAULT_COUNTRY);

  useEffect(() => {
    const setInitialCountry = async () => {
      const country = await getRandomCountry();
      setCountry(country);
    };

    setInitialCountry();
  }, []);

  return null;
};
```

---

# Encapsular en función `useRandomCountry`

```js
import { useState, useEffect } from "react";
import "./App.css";

const getRandomCountry = async () => {
  const response = await fetch(
    "https://random-data-api.com/api/v2/addresses"
  );

  const country = await response.json();

  return {
    name: country.country,
    code: country.country_code,
  };
};

const DEFAULT_COUNTRY = {
  name: "Spain",
  code: "ES",
};

const useRandomCountry = (
  defaultCountry = DEFAULT_COUNTRY
) => {
  const [country, setCountry] = useState(defaultCountry);

  useEffect(() => {
    const setInitialCountry = async () => {
      const country = await getRandomCountry();
      setCountry(country);
    };

    setInitialCountry();
  }, []);

  return { country };
};
```

---

layout: two-cols
---

# Extracción a otro fichero

::left::

## Antes

```js {4-34,54-55}
import { useState, useEffect } from "react";
import "./App.css";

const getRandomCountry = async () => {
  const response = await fetch(
    "https://random-data-api.com/api/v2/addresses"
  );

  const country = await response.json();

  return {
    name: country.country,
    code: country.country_code,
  };
};

const DEFAULT_COUNTRY = {
  name: "Spain",
  code: "ES",
};

const useRandomCountry = (
  defaultCountry = DEFAULT_COUNTRY
) => {
  const [country, setCountry] = useState(defaultCountry);

  useEffect(() => {
    const setInitialCountry = async () => {
      const country = await getRandomCountry();
      setCountry(country);
    };

    setInitialCountry();
  }, []);

  return { country };
};

const App = () => {
  const { country } = useRandomCountry();

  return null;
};
```

::right::

## Después

```js
import { useState } from "react";
import "./App.css";
import useRandomCountry from "./hooks/useRandomCountry";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  { name: "Danerys Targaryen", price: 16.5 },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const { country } = useRandomCountry();

  return null;
};
```

```js
// file: hooks/useRandomCountry.js

import { useState, useEffect } from "react";

const getRandomCountry = async () => {
  const response = await fetch(
    "https://random-data-api.com/api/v2/addresses"
  );

  const country = await response.json();

  return {
    name: country.country,
    code: country.country_code,
  };
};

const DEFAULT_COUNTRY = {
  name: "Spain",
  code: "ES",
};

const useRandomCountry = (
  defaultCountry = DEFAULT_COUNTRY
) => {
  const [country, setCountry] = useState(defaultCountry);

  useEffect(() => {
    const setInitialCountry = async () => {
      const country = await getRandomCountry();
      setCountry(country);
    };

    setInitialCountry();
  }, []);

  return { country };
};

export default useRandomCountry;
```

---

layout: two-cols
---

# Hook `useTaxes`

::left::

## Lógica de impuestos en `App`

```js {25,31-39}
import { useState } from "react";
import "./App.css";
import useRandomCountry from "./hooks/useRandomCountry";

const App = () => {
  const { country } = useRandomCountry();

  const countryLetter = country.name
    .charAt(0)
    .toLowerCase();

  const taxPercentage = ["a", "e", "i", "o", "u"].includes(
    countryLetter
  )
    ? 10
    : 20;

  return null;
};
```

::right::

## Extraída a `useTaxes`

```js
// file: hooks/useTaxes.js

import useRandomCountry from "./useRandomCountry";

const useTaxes = () => {
  const { country } = useRandomCountry();

  const countryLetter = country.name
    .charAt(0)
    .toLowerCase();

  const taxPercentage = ["a", "e", "i", "o", "u"].includes(
    countryLetter
  )
    ? 10
    : 20;

  return { taxPercentage, country };
};

export default useTaxes;
```

---

# Uso de `useTaxes`

```js {3|25}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";

const App = () => {
  const { taxPercentage, country } = useTaxes();

  return (
    <>
      <div>
        Impuesto: <span>{taxPercentage}%</span>
        <span>
          {country?.code && (
            {`https://flagsapi.com/${country.code}/flat/64.png`}
          )}
        </span>
      </div>
    </>
  );
};
```

---

layout: two-cols
---

# Opción de dragón

::left::

## Por índice

```js {20,28}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  { name: "Danerys Targaryen", price: 16.5 },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const [
    selectedCharacterIndex,
    setSelectedCharacterIndex,
  ] = useState(0);

  const selectedCharacter =
    characters[selectedCharacterIndex];

  const showDragonOption = selectedCharacterIndex === 1;
  const [includeDragon, setIncludeDragon] = useState(false);

  const { taxPercentage, country } = useTaxes();

  const extraDragon =
    showDragonOption && includeDragon ? DRAGON_PRICE : 0;

  return null;
};
```

::right::

## Por datos

```js {8-12,31}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  {
    name: "Danerys Targaryen",
    price: 16.5,
    dragonOption: true,
  },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const [
    selectedCharacterIndex,
    setSelectedCharacterIndex,
  ] = useState(0);

  const selectedCharacter =
    characters[selectedCharacterIndex];

  const [includeDragon, setIncludeDragon] = useState(false);

  const { taxPercentage, country } = useTaxes();

  const extraDragon =
    selectedCharacter.dragonOption && includeDragon
      ? DRAGON_PRICE
      : 0;

  return null;
};
```

---

# Condición en datos

```js
const characters = [
  { name: "Jon Snow", price: 13.5 },
  {
    name: "Danerys Targaryen",
    price: 16.5,
    dragonOption: true,
  },
  { name: "Tyrion Lannister", price: 19 },
];
```

<div class="text-left text-2xl mt-8">

- <span v-click>Evita depender de posiciones mágicas</span>
- <span v-click>El modelo describe el comportamiento</span>
- <span v-click>El render se vuelve más declarativo</span>

</div>

---

layout: two-cols
---

# Estado `selectedCharacter`

::left::

## Índice como estado

```js {17-22}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  {
    name: "Danerys Targaryen",
    price: 16.5,
    dragonOption: true,
  },
  { name: "Tyrion Lannister", price: 19 },
];

const App = () => {
  const [
    selectedCharacterIndex,
    setSelectedCharacterIndex,
  ] = useState(0);

  const selectedCharacter =
    characters[selectedCharacterIndex];

  return null;
};
```

::right::

## Objeto como estado

```js {17-19}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  {
    name: "Danerys Targaryen",
    price: 16.5,
    dragonOption: true,
  },
  { name: "Tyrion Lannister", price: 19 },
];

const App = () => {
  const [selectedCharacter, setSelectedCharacter] =
    useState(characters[0]);

  return null;
};
```

---

# Cambio en el evento

```js {1-5|8-16}
<select
  onChange={(e) =>
    setSelectedCharacter(
      characters[Number(e.target.value)]
    )
  }
>
  {characters.map((character, index) => {
    return (
      <option
        key={character.name}
        value={index}
      >
        {`${character.name} (${character.price}€)`}
      </option>
    );
  })}
</select>
```

---

layout: two-cols
---

# Estado complejo: `selectedCharacter` + `includeDragon`

::left::

## Estado separado

```js {17-20}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  {
    name: "Danerys Targaryen",
    price: 16.5,
    dragonOption: true,
  },
  { name: "Tyrion Lannister", price: 19 },
];

const App = () => {
  const [selectedCharacter, setSelectedCharacter] =
    useState(characters[0]);

  const [includeDragon, setIncludeDragon] = useState(false);

  return null;
};
```

::right::

## Producto como estado

```js {17-20,26-27}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  {
    name: "Danerys Targaryen",
    price: 16.5,
    dragonOption: true,
  },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const [selectedProduct, setSelectedProduct] = useState({
    character: characters[0],
    includeDragon: false,
  });

  const { character: selectedCharacter, includeDragon } =
    selectedProduct;

  return null;
};
```

---

# Producto seleccionado

```js {1-4|6-7|9-13}
const [selectedProduct, setSelectedProduct] = useState({
  character: characters[0],
  includeDragon: false,
});

const { character: selectedCharacter, includeDragon } =
  selectedProduct;

const extraDragon =
  selectedCharacter.dragonOption && includeDragon
    ? DRAGON_PRICE
    : 0;
```

---

# Encapsular `handleCharacterSelected`

```js {1-9}
const handleCharacterSelected = (e) => {
  const selectedCharacter =
    characters[Number(e.target.value)];

  setSelectedProduct({
    character: selectedCharacter,
    includeDragon: false,
  });
};
```

---

# Encapsular `handleIncludeDragonChanged`

```js {1-7}
const handleIncludeDragonChanged = (e) => {
  setSelectedProduct({
    character: selectedProduct.character,
    includeDragon: e.target.checked,
  });
};
```

---

# Render usando handlers

```jsx {1|16}
<select onChange={handleCharacterSelected}>
  {characters.map((character, index) => {
    return (
      <option
        key={character.name}
        value={index}
      >
        {`${character.name} (${character.price}€)`}
      </option>
    );
  })}
</select>

<input
  type="checkbox"
  checked={includeDragon}
  onChange={handleIncludeDragonChanged}
/>
```

---

layout: two-cols
---

# Hook `useProduct`

::left::

## Antes

```js {5-15}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";

const characters = [
  { name: "Jon Snow", price: 13.5 },
  {
    name: "Danerys Targaryen",
    price: 16.5,
    dragonOption: true,
  },
  { name: "Tyrion Lannister", price: 19 },
];

const DRAGON_PRICE = 44.5;

const App = () => {
  const [selectedProduct, setSelectedProduct] = useState({
    character: characters[0],
    includeDragon: false,
  });

  return null;
};
```

::right::

## Después

```js {4-8|11-15}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";
import {
  characters,
  DRAGON_PRICE,
} from "./model/characters";
import useProduct from "./hooks/useProduct";

const App = () => {
  const {
    product: selectedProduct,
    setProduct: setSelectedProduct,
    productPrice: selectedProductPrice,
  } = useProduct();

  return null;
};
```

---

# Modelo `characters`

```js
// file: model/characters.js

export const characters = [
  { name: "Jon Snow", price: 13.5 },
  {
    name: "Danerys Targaryen",
    price: 16.5,
    dragonOption: true,
  },
  { name: "Tyrion Lannister", price: 19 },
];

export const DRAGON_PRICE = 44.5;
```

---

# Hook `useProduct`

```js {1|3-6|9-12|14-18|20-24}
import { useState } from "react";
import {
  characters,
  DRAGON_PRICE,
} from "../model/characters";

const useProduct = () => {
  const [product, setProduct] = useState({
    character: characters[0],
    includeDragon: false,
  });

  const extraDragon =
    product.character.dragonOption && product.includeDragon
      ? DRAGON_PRICE
      : 0;

  const productPrice =
    product.character.price + extraDragon;

  return {
    product,
    productPrice,
    setProduct,
  };
};

export default useProduct;
```

---

# Qué aporta `useProduct`

<div class="text-left text-2xl mt-10">

- <span v-click>Encapsula el estado del producto</span>
- <span v-click>Centraliza el cálculo del precio</span>
- <span v-click>Reduce lógica en `App`</span>
- <span v-click>Facilita extraer componentes visuales</span>

</div>

---

layout: two-cols
---

# Componente `ProductSelector`

::left::

## Antes en `App`

```js {28-42,48-73}
const handleCharacterSelected = (e) => {
  const selectedCharacter =
    characters[Number(e.target.value)];

  setSelectedProduct({
    character: selectedCharacter,
    includeDragon: false,
  });
};

const handleIncludeDragonChanged = (e) => {
  setSelectedProduct({
    character: selectedProduct.character,
    includeDragon: e.target.checked,
  });
};

return (
  <>
    <div>
      <label>Elige un personaje:</label>
      <select onChange={handleCharacterSelected}>
        {characters.map((character, index) => {
          return (
            <option
              key={character.name}
              value={index}
            >
              {`${character.name} (${character.price}€)`}
            </option>
          );
        })}
      </select>
    </div>

    {selectedCharacter.dragonOption && (
      <div>
        <input
          type="checkbox"
          checked={includeDragon}
          onChange={handleIncludeDragonChanged}
        />
      </div>
    )}
  </>
);
```

::right::

## Después en `App`

```jsx {24-27}
import ProductSelector from "./components/ProductSelector";

const App = () => {
  const {
    product: selectedProduct,
    setProduct: setSelectedProduct,
    productPrice: selectedProductPrice,
  } = useProduct();

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>

      <ProductSelector
        selectedProduct={selectedProduct}
        onProductSelected={setSelectedProduct}
      />
    </>
  );
};
```

---

# `ProductSelector`

```js {1-4|8-11|13-21|25-54}
import {
  characters,
  DRAGON_PRICE,
} from "../model/characters";
import PropTypes from "prop-types";

const ProductSelector = ({
  selectedProduct,
  onProductSelected,
}) => {
  const handleCharacterSelected = (e) => {
    const selectedCharacter =
      characters[Number(e.target.value)];

    onProductSelected({
      character: selectedCharacter,
      includeDragon: false,
    });
  };

  const handleIncludeDragonChanged = (e) => {
    onProductSelected({
      character: selectedProduct.character,
      includeDragon: e.target.checked,
    });
  };

  return (
    <div>
      <label>Elige un personaje:</label>
      <select onChange={handleCharacterSelected}>
        {characters.map((character, index) => {
          return (
            <option
              key={character.name}
              value={index}
            >
              {`${character.name} (${character.price}€)`}
            </option>
          );
        })}
      </select>

      {selectedProduct.character.dragonOption && (
        <div>
          <input
            type="checkbox"
            checked={selectedProduct.includeDragon}
            onChange={handleIncludeDragonChanged}
          />
          <label htmlFor="dragon">
            Con dragón en el hombro ({DRAGON_PRICE}€ más)
          </label>
        </div>
      )}
    </div>
  );
};
```

---

# `ProductSelector.propTypes`

```js {1-15}
ProductSelector.propTypes = {
  selectedProduct: PropTypes.shape({
    character: PropTypes.shape({
      name: PropTypes.string.isRequired,
      price: PropTypes.number.isRequired,
      dragonOption: PropTypes.bool,
    }),
    includeDragon: PropTypes.bool.isRequired,
  }),

  onProductSelected: PropTypes.func,
};

export default ProductSelector;
```

---

# Resultado del refactor

```txt
src/
├─ App.jsx
├─ components/
│  └─ ProductSelector.jsx
├─ hooks/
│  ├─ useProduct.js
│  ├─ useRandomCountry.js
│  └─ useTaxes.js
└─ model/
   └─ characters.js
```

---

# `App` final

```js {4-5|8-15|17-21|24-27|33-41|44-55|58-62}
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";
import useProduct from "./hooks/useProduct";
import ProductSelector from "./components/ProductSelector";

const App = () => {
  const {
    product: selectedProduct,
    setProduct: setSelectedProduct,
    productPrice: selectedProductPrice,
  } = useProduct();

  const [quantity, setQuantity] = useState(1);

  const { taxPercentage, country } = useTaxes();

  const totalPrice = selectedProductPrice * quantity;

  const totalPriceValueWithTaxes =
    totalPrice + totalPrice * (taxPercentage / 100);

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>

      <ProductSelector
        selectedProduct={selectedProduct}
        onProductSelected={setSelectedProduct}
      />

      <div>
        <label htmlFor="quantity">Cantidad:</label>
        <input
          type="number"
          min="1"
          value={quantity}
          onChange={(e) =>
            setQuantity(Number(e.target.value))
          }
        />
      </div>

      <div>
        Impuesto: <span>{taxPercentage}%</span>
        <span>
          {country?.code && (
            {`https://flagsapi.com/${country.code}/flat/64.png`}
          )}
        </span>
      </div>

      <div>
        Precio total:{" "}
        <span>{totalPriceValueWithTaxes.toFixed(2)}€</span>
      </div>
    </>
  );
};

export default App;
```

---

# Repaso patrón React ⚛️

<div class="text-left text-3xl mt-10">

- Estados (`useState`)
- Efectos (`useEffect`)
- Cálculos JavaScript
- Render JSX data driven

</div>

```js showNumbers
import { useState } from "react";
import "./App.css";
import useTaxes from "./hooks/useTaxes";
import useProduct from "./hooks/useProduct";
import ProductSelector from "./components/ProductSelector";

const App = () => {
  const {
    product: selectedProduct,
    setProduct: setSelectedProduct,
    productPrice: selectedProductPrice,
  } = useProduct();

  const [quantity, setQuantity] = useState(1);
  const { taxPercentage, country } = useTaxes();

  const totalPrice = selectedProductPrice * quantity;
  const totalPriceValueWithTaxes =
    totalPrice + totalPrice * (taxPercentage / 100);

  return (
    <>
      <h1>Calculadora de precios de muñecos cabezones</h1>

      <ProductSelector
        selectedProduct={selectedProduct}
        onProductSelected={setSelectedProduct}
      />

      <div>
        <label htmlFor="quantity">Cantidad:</label>
        <input
          type="number"
          min="1"
          value={quantity}
          onChange={(e) =>
            setQuantity(Number(e.target.value))
          }
        />
      </div>

      <div>
        Impuesto: <span>{taxPercentage}%</span>
      </div>

      <div>
        Precio total:{" "}
        <span>{totalPriceValueWithTaxes.toFixed(2)}€</span>
      </div>
    </>
  );
};
```

---

# Estados + efectos ⇒ Custom Hooks

<div class="text-left text-3xl mt-10">

- <span class="text-yellow-300 font-bold italic">
    Estados (`useState`) + Efectos (`useEffect`) ⇒ <ins>Custom Hooks</ins>
  </span>
- Cálculos JavaScript
- Render JSX data driven

</div>

```js {8-14}
const App = () => {
  const {
    product: selectedProduct,
    setProduct: setSelectedProduct,
    productPrice: selectedProductPrice,
  } = useProduct();

  const [quantity, setQuantity] = useState(1);
  const { taxPercentage, country } = useTaxes();

  // ...
};
```

---

# Cálculos JavaScript

<div class="text-left text-3xl mt-10">

- Estados (`useState`) + Efectos (`useEffect`) ⇒ Custom Hooks
- <span class="text-yellow-300 font-bold italic">
    Cálculos JavaScript
  </span>
- Render JSX data driven

</div>

```js {16-18}
const totalPrice = selectedProductPrice * quantity;

const totalPriceValueWithTaxes =
  totalPrice + totalPrice * (taxPercentage / 100);
```

---

# Render JSX data driven

<div class="text-left text-3xl mt-10">

- Estados (`useState`) + Efectos (`useEffect`) ⇒ Custom Hooks
- Cálculos JavaScript
- <span class="text-yellow-300 font-bold italic">
    Render JSX data driven
  </span>

</div>

```jsx {21-57}
return (
  <>
    <h1>Calculadora de precios de muñecos cabezones</h1>

    <ProductSelector
      selectedProduct={selectedProduct}
      onProductSelected={setSelectedProduct}
    />

    <div>
      <label htmlFor="quantity">Cantidad:</label>
      <input
        type="number"
        min="1"
        value={quantity}
        onChange={(e) =>
          setQuantity(Number(e.target.value))
        }
      />
    </div>

    <div>
      Precio total:{" "}
      <span>{totalPriceValueWithTaxes.toFixed(2)}€</span>
    </div>
  </>
);
```

---

# Render ⇒ Componentes

<div class="text-left text-3xl mt-10">

- Estados (`useState`) + Efectos (`useEffect`) ⇒ Custom Hooks
- Cálculos JavaScript
- <span class="text-yellow-300 font-bold italic">
    Render JSX data driven ⇒ <u>Componentes</u>
  </span>

</div>

```jsx {24-27}
<ProductSelector
  selectedProduct={selectedProduct}
  onProductSelected={setSelectedProduct}
/>
```

---

# Patrón final

<div class="text-left text-2xl mt-12">

```txt
Estados + efectos  ──────>  Custom Hooks
Cálculos           ──────>  Datos derivados
Render             ──────>  Componentes
Modelo             ──────>  Datos explícitos
```

</div>

---

# Happy React Coding ❤️⚛️!

<div class="text-5xl mt-20">

Gracias 🙌

</div>
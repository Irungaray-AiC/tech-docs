## Antes de desarrollar, tener en cuenta:
- Pensar 'Esto es testeable?' 'Cómo testearía esto?'

- Cuánto puede escalar un archivo. Idealmente, tendríamos un componente abuelo (page) que, por context, provea de props a toda su descendencia.

- Al componentizar, tener en mente smart y dummy components

- Pensar 'Es mi código fácil de leer para el próximo que lo edite?'

- Pensar 'Será mi código fácil de leer para mi yo del futuro?'

## Lineamientos generales:

- Todo lo que pueda no estar en el JSX, no debería estar en el JSX **(1)**

- Todo lo que pueda estar en la hoja de estilos, debería estar en la hoja de estilos **(2)**

- Todo lo que pueda ser mapeado, debería ser mapeado (en la medida que no afecte la legibilidad) **(3)**

- Todo lo que pueda no estar hardcodeado, no debería estar hardcodeado


## A rajatabla:

- Ordenar imports

- No debe haber más de UN componente por archivo

- Al pasar props, destructurarlas en el componente hijo para identificar cuáles estan en uso y cuáles no

Es recomendable también usar PropTypes. Además de proveer un mínimo tipado, nos facilita el autocompletado

- Escribir funciones handlers con un nombre significativo en lugar de inline functions **(4)**

- Estilos en línea: más de un atributo es inaceptable

- Destructurar estilos

- Evitar ternarios anidados (componentizar e implementar early return)

## Desarrollando:

- Evitar tener demasiados useState. Siempre que sea posible, manejar estados con un 'enum' **(4)**

- Preferir setear estados en base al retorno de funciones puras, en lugar de setear estados dentro de funciones **(5)**

- Siempre que sea posible, evitar ternarios en el JSX. Preferir AND (&&)

- De ser necesario usar ternarios, (en lo posible) extraer a constantes.

- Usar try/catch en vez de promises

- Idealmente, el JSX no debería estar indentado mas de 5 tabs

- Siempre que sea posible, usar el patrón early return para evitar ifs anidados

- No escatimar saltos de linea (a consciencia)

- Usar initialValues en estados


## En un mundo ideal:

- Usar etiquetas semánticas

- No pasar props más de 2 componentes hacia abajo

- Controlar useEffects. Si una request se hace más de una vez, revisar

- Más de 2 (o 3) useEffects es demasiado

- Habiendo más de 250 líneas en un componente, vale la pena plantear componentización

- En la medida que justifique, al encontrar un componente legacy que no cumpla estos lineamientos, plantear una refactorización

<br />
<br />

<div align="center">

### [**Bad Component**](https://github.com/Irungaray-AiC/tech-docs/blob/main/BadComponent.jsx) - [**Good Component**](https://github.com/Irungaray-AiC/tech-docs/blob/main/GoodComponent.jsx)

### [**Anatomy of a Bad Component**](https://github.com/Irungaray-AiC/tech-docs/blob/main/BadComponent.jsx) - [**Anatomy of a Good Component**](https://github.com/Irungaray-AiC/tech-docs/blob/main/GoodComponent.jsx)

</div>

<br />
<br />

## Ejemplos

### Todo lo que pueda no estar en el JSX, no debería estar en el JSX (1)
##### BAD:
```javascript
const SomeComponent = () => {

    const calcSomething = () => {
        return 1 + 1;
    };

    return (
        <Box>Calculation of something: {calcSomething()}</Box>
    );

};
```

##### GOOD:
```javascript
import { calcSomething } from '../helpers/funcs';

const SomeComponent = () => {

    const somethingCalculated = calcSomething();

    return (
        <Box>Calculation of something: {somethingCalculated}</Box>
    );

};
```

### Todo lo que pueda estar en la hoja de estilos, debería estar en la hoja de estilos (2)
##### BAD:
```javascript
const SomeComponent = () => {

    const isLessThan1300pxWidth = useMediaQuery({ query: '(max-width: 1300px)' });

    return (
        <Box className={isLessThan1300pxWidth ? _textMobile :  _textDesktop}>
            Some text
        </Box>
    );

};
```

##### GOOD:
```javascript
const SomeComponent = () => {

    const isLessThan1300pxWidth = useMediaQuery({ query: '(max-width: 1300px)' });

    const { _text } = useStyles({ isLessThan1300pxWidth });

    return (
        <Box className={_text}>
            Some text
        </Box>
    );

};
```

### Todo lo que pueda ser mapeado, debería ser mapeado (3)
##### BAD:
```javascript
const SomeComponent = () => {

    return (
        <TableHead>
            <TableRow>
                <TableCell>ID</TableCell>
                <TableCell>Fecha de Reserva</TableCell>
                <TableCell>Concepto</TableCell>
                <TableCell>Booking ID</TableCell>
                <TableCell>Medio de Pago</TableCell>
            </TableRow>
        </TableHead>
    );

};
```

##### GOOD:
```javascript
const SomeComponent = () => {

    const tableHead = [ 'ID', 'Fecha de Reserva', 'Concepto', 'Booking ID', 'Medio de Pago'];

    return (
        <TableHead>
            <TableRow>
                {tableHead.map((head, i) => (
                    <TableCell key={i}>{head}</TableCell>
                ))}
            </TableRow>
        </TableHead>
    );

};
```

### Evitar tener demasiados useState. (4)
##### BAD:
```javascript
const SomeComponent = () => {

    const [isPaymentModalOpen, setIsPaymentModalOpen] = useState(false)
    const [isEmployeesModalOpen, setIsEmployeesModalOpen] = useState(false)

    return (
        <Box>
            <Button onClick={() => setIsPaymentModalOpen(true)}>Pagos</Button>
            <Button onClick={() => setIsEmployeesModalOpen(true)}>Empleados</Button>
        </Box>
    );

};
```

##### GOOD:
```javascript
const SomeComponent = () => {

    const [modalOpen, setModalOpen] = useState(ModalOpenEnum.NONE)

    const handleOpenModal = (key) =>  setModalOpen(key)

    return (
        <Box>
            <Button onClick={() => handleOpenModal(ModalOpenEnum.PAYMENT)}>Pagos</Button>
            <Button onClick={() => handleOpenModal(ModalOpenEnum.EMPLOYEES)}>Empleados</Button>
        </Box>
    );

};

const ModalOpenEnum = {
    NONE: null,
    PAYMENT: 'PAYMENT',
    EMPLOYEES: 'EMPLOYEES',
}
```

### Preferir setear estados en base al retorno de funciones puras, en lugar de setear estados dentro de funciones. (5)
##### BAD:
```javascript
const SomeComponent = () => {

    const [peopleQuantity, setPeopleQuantity] = useState(1)
    const [total, setTotal] = useState(0)

    const calcTotal = (quantity) => {
        setTotal(price * quantity + fee)
    }

    useEffect(() => {
        calcTotal(peopleQuantity)
    }, [peopleQuantity])

    return (
        <Box>
            ...
        </Box>
    )
}
```

##### GOOD:
```javascript
import { calcTotal } from '../helpers/funcs'

const SomeComponent = () => {

    const [peopleQuantity, setPeopleQuantity] = useState(1)
    const [total, setTotal] = useState(0)

    useEffect(() => {
        const args = { spacePrice, peopleQuantity, fee }
        const totalCalc = calcTotal(args)

        setTotal(totalCalc)
    }, [peopleQuantity])

    return (
        <Box>
            ...
        </Box>
    )

}

// ../helpers/funcs/calcTotal.js
const calcTotal = (params) => {
    const  { price, quantity, fee } = params;

    return price * quantity + fee
}

// De esta manera podemos extraer lógica del componente
// para (entre otras cosas) facilitar la lectura
```

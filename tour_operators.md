# Работа с Neo4j и оптимизация запрсов с помощью индексов

## 1. Создание узлов туроператоров

```cypher
CREATE (:TourOperator {name: 'Tez Tour'})
CREATE (:TourOperator {name: 'Anex Tour'})
CREATE (:TourOperator {name: 'Pegas Touristik'})
CREATE (:TourOperator {name: 'Coral Travel'})
CREATE (:TourOperator {name: 'Biblio Globus'});
```

## 2. Создание направлений (Страна - Конкретное место)

```cypher
CREATE (:Country {name: 'Thailand'})-[:HAS_DESTINATION]->(:City {name: 'Bangkok'})
CREATE (:Country {name: 'Thailand'})-[:HAS_DESTINATION]->(:City {name: 'Phuket'})
CREATE (:Country {name: 'Egypt'})-[:HAS_DESTINATION]->(:City {name: 'Sharm El Sheikh'})
CREATE (:Country {name: 'Egypt'})-[:HAS_DESTINATION]->(:City {name: 'Hurghada'})
CREATE (:Country {name: 'Turkey'})-[:HAS_DESTINATION]->(:City {name: 'Antalya'})
CREATE (:Country {name: 'Turkey'})-[:HAS_DESTINATION]->(:City {name: 'Istanbul'})
CREATE (:Country {name: 'Greece'})-[:HAS_DESTINATION]->(:City {name: 'Athens'})
CREATE (:Country {name: 'Greece'})-[:HAS_DESTINATION]->(:City {name: 'Crete'})
CREATE (:Country {name: 'Italy'})-[:HAS_DESTINATION]->(:City {name: 'Rome'})
CREATE (:Country {name: 'Italy'})-[:HAS_DESTINATION]->(:City {name: 'Milan'})
CREATE (:Country {name: 'Spain'})-[:HAS_DESTINATION]->(:City {name: 'Barcelona'})
CREATE (:Country {name: 'Spain'})-[:HAS_DESTINATION]->(:City {name: 'Madrid'})
CREATE (:Country {name: 'Vietnam'})-[:HAS_DESTINATION]->(:City {name: 'Hanoi'})
CREATE (:Country {name: 'Maldives'})-[:HAS_DESTINATION]->(:City {name: 'Male'})
CREATE (:Country {name: 'UAE'})-[:HAS_DESTINATION]->(:City {name: 'Dubai'})
CREATE (:Country {name: 'Cuba'})-[:HAS_DESTINATION]->(:City {name: 'Havana'});
```

## 3. Связи между туроператорами и странами, в которые они предлагают путевки

```cypher
MATCH (to:TourOperator {name: 'Tez Tour'}), (c:Country {name: 'Thailand'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Tez Tour'}), (c:Country {name: 'Egypt'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Tez Tour'}), (c:Country {name: 'Turkey'}) CREATE (to)-[:OFFERS_TO]->(c);

MATCH (to:TourOperator {name: 'Anex Tour'}), (c:Country {name: 'Turkey'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Anex Tour'}), (c:Country {name: 'Egypt'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Anex Tour'}), (c:Country {name: 'Spain'}) CREATE (to)-[:OFFERS_TO]->(c);

MATCH (to:TourOperator {name: 'Pegas Touristik'}), (c:Country {name: 'Vietnam'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Pegas Touristik'}), (c:Country {name: 'Thailand'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Pegas Touristik'}), (c:Country {name: 'Maldives'}) CREATE (to)-[:OFFERS_TO]->(c);

MATCH (to:TourOperator {name: 'Coral Travel'}), (c:Country {name: 'Greece'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Coral Travel'}), (c:Country {name: 'Italy'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Coral Travel'}), (c:Country {name: 'UAE'}) CREATE (to)-[:OFFERS_TO]->(c);

MATCH (to:TourOperator {name: 'Biblio Globus'}), (c:Country {name: 'Cuba'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Biblio Globus'}), (c:Country {name: 'Greece'}) CREATE (to)-[:OFFERS_TO]->(c);
MATCH (to:TourOperator {name: 'Biblio Globus'}), (c:Country {name: 'Italy'}) CREATE (to)-[:OFFERS_TO]->(c);
```

## 4. Создание дополнительных городов, которые могут быть транспортными узлами

```cypher
CREATE (:City {name: 'Berlin', hasAirport: true, hasTrainStation: true})
CREATE (:City {name: 'Frankfurt', hasAirport: true, hasTrainStation: true})
CREATE (:City {name: 'Paris', hasAirport: true, hasTrainStation: true})
CREATE (:City {name: 'Warsaw', hasAirport: false, hasTrainStation: true})
CREATE (:City {name: 'Prague', hasAirport: false, hasTrainStation: true})
CREATE (:City {name: 'Vienna', hasAirport: true, hasTrainStation: true});
```

## 5. Наземные маршруты (поезд, автобус)

```cypher
MATCH (c1:City {name: 'Berlin'}), (c2:City {name: 'Warsaw'})
CREATE (c1)-[:TRAVELS_BY {transportType: 'train', durationHours: 6}]->(c2)
CREATE (c2)-[:TRAVELS_BY {transportType: 'bus', durationHours: 8}]->(c1);

MATCH (c1:City {name: 'Berlin'}), (c2:City {name: 'Prague'})
CREATE (c1)-[:TRAVELS_BY {transportType: 'train', durationHours: 4}]->(c2)
CREATE (c2)-[:TRAVELS_BY {transportType: 'bus', durationHours: 5}]->(c1);

MATCH (c1:City {name: 'Prague'}), (c2:City {name: 'Vienna'})
CREATE (c1)-[:TRAVELS_BY {transportType: 'train', durationHours: 4}]->(c2)
CREATE (c2)-[:TRAVELS_BY {transportType: 'bus', durationHours: 4.5}]->(c1);

MATCH (c1:City {name: 'Rome'}), (c2:City {name: 'Milan'})
CREATE (c1)-[:TRAVELS_BY {transportType: 'train', durationHours: 3}]->(c2);
```

## 6. Воздушные маршруты

```cypher
MATCH (c1:City {name: 'Paris'}), (c2:City {name: 'Bangkok'})
CREATE (c1)-[:TRAVELS_BY {transportType: 'plane', durationHours: 11}]->(c2);

MATCH (c1:City {name: 'Berlin'}), (c2:City {name: 'Sharm El Sheikh'})
CREATE (c1)-[:TRAVELS_BY {transportType: 'plane', durationHours: 4.5}]->(c2);
```

## 7. Вывод направлений, состоящих только из наземного транспорта (поезд, автобус)

```cypher
MATCH p=(startNode:City)-[r:TRAVELS_BY*1..5]->(endNode:City)
WHERE ALL(rel IN relationships(p) WHERE rel.transportType IN ['train', 'bus'])
RETURN p, startNode.name, endNode.name
ORDER BY length(p) ASC;
```

## 8. Вывод плана запроса

```cypher
EXPLAIN
MATCH p=(startNode:City)-[r:TRAVELS_BY*1..5]->(endNode:City)
WHERE ALL(rel IN relationships(p) WHERE rel.transportType IN ['train', 'bus'])
RETURN p, startNode.name, endNode.name
ORDER BY length(p) ASC;
```

или

```cypher
PROFILE
MATCH p=(startNode:City)-[r:TRAVELS_BY*1..5]->(endNode:City)
WHERE ALL(rel IN relationships(p) WHERE rel.transportType IN ['train', 'bus'])
RETURN p, startNode.name, endNode.name
ORDER BY length(p) ASC;
```

## 9. Добавление индексов

**Индекс на свойство 'name' для узлов типа 'City':**

```cypher
CREATE INDEX FOR (n:City) ON (n.name);
```

**Индекс на свойство 'name' для узлов типа 'Country':**

```cypher
CREATE INDEX FOR (n:Country) ON (n.name);
```

**Индекс на свойство 'name' для узлов типа 'TourOperator':**

```cypher
CREATE INDEX FOR (n:TourOperator) ON (n.name);
```

## 10. Повторный вывод плана запроса

```cypher
EXPLAIN
MATCH p=(startNode:City)-[r:TRAVELS_BY*1..5]->(endNode:City)
WHERE ALL(rel IN relationships(p) WHERE rel.transportType IN ['train', 'bus'])
RETURN p, startNode.name, endNode.name
ORDER BY length(p) ASC;
```

или

```cypher
PROFILE
MATCH p=(startNode:City)-[r:TRAVELS_BY*1..5]->(endNode:City)
WHERE ALL(rel IN relationships(p) WHERE rel.transportType IN ['train', 'bus'])
RETURN p, startNode.name, endNode.name
ORDER BY length(p) ASC;
```

## Вывод

**После добавления индексов:** 

- операции, связанные с поиском по имени города, стали использовать индекс (NodeByLabelScan изменился на NodeIndexSeek);
- количество db hits для операций, связанных с поиском или фильтрацией по индексированным свойствам значительно уменьшилось.

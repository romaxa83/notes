#python #package #geo

- GeoPandas Basics: Maps, Projections, and Spatial Joins - https://realpython.com/geopandas/#:~:text=GeoPandas%20extends%20pandas%20to%20make,GeoPandas%20is%20an%20excellent%20choice.
- [Визуализация данных GeoJSON с использованием GeoPandas и Python](https://habr.com/ru/articles/680100/)
- https://github.com/geopandas/geopandas/tree/main/doc/source/docs

**[GeoPandas](https://geopandas.org/)** — это ==open-source библиотека для Python, которая позволяет легко работать с геопространственными данными==

Основные возможности:
- **Работа с гео-объектами:** вы можете хранить и обрабатывать координаты прямо в таблице (структуры `GeoSeries` и `GeoDataFrame`).
- **Пространственные операции:** расчет расстояний, площадей, пересечений и объединений объектов (через интеграцию с библиотекой `shapely`).
- **Работа с форматами:** чтение и запись файлов Shapefile, GeoJSON, KML, GeoPackage и других.
- **Визуализация:** быстрое построение карт одной командой `.plot()` благодаря интеграции с `matplotlib`.
- **Проекции:** легкое управление системами координат (CRS) и пересчет данных между ними.
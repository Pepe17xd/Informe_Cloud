# Fuentes ER PostgreSQL
- Identity: `sources/identity/app/models.py`, entidad `User`: tabla users, UUID PK, email único/indexado.
- Catalog: `sources/catalog/src/main/java/com/cinemaclub/catalog/model/*.java`: `movie`, `genre`, `artist`, `movie_video_source`, `movie_subtitle`; relaciones se deben leer desde las anotaciones JPA.

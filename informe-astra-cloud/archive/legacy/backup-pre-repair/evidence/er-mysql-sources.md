# Fuentes ER MySQL
- `sources/community/app/models/user.py`, `club.py`, `membership.py`, `watch_room.py`.
- FK verificadas: clubs.owner_id; memberships.user_id/club_id; watch_rooms.club_id/host_user_id; watch_participants.watch_room_id/user_id.

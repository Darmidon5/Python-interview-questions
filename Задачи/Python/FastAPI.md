1) джун принес на ревью. критикуй
@app.get("/users")
async def get_users(
  user_id: Annotated[int | None, Query()] = None,
) -> list[User] | User:
  async with async_session_maker.begin() as session:
      stmt = select(User).where(User.is_active.is_(True))
      if user_id is not None:
          stmt = stmt.where(User.id == user_id)
          return await session.scalar(stmt)
      return (await session.scalars(stmt)).all()

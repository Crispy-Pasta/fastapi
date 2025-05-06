from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from typing import List
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# Database configuration
DATABASE_URL = "mysql+pymysql://username:password@localhost:3306/mydatabase"

# SQLAlchemy setup
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

#test abc

#tesdt ulit

# Define table model
class Item(Base):
    __tablename__ = "items"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String(255), nullable=False)
    description = Column(String(255), nullable=True)

Base.metadata.create_all(bind=engine)

# Pydantic model
class ItemCreate(BaseModel):
    name: str
    description: str = None

class ItemOut(ItemCreate):
    id: int

# FastAPI app
app = FastAPI()

# Dependency to get DB session
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

# POST endpoint to create item
@app.post("/items/", response_model=ItemOut)
def create_item(item: ItemCreate, db=next(get_db())):
    db_item = Item(**item.dict())
    db.add(db_item)
    db.commit()
    db.refresh(db_item)
    return db_item

# GET endpoint to list all items
@app.get("/items/", response_model=List[ItemOut])
def read_items(db=next(get_db())):
    items = db.query(Item).all()
    return items

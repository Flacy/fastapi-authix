A key feature of this library is its ability to serialize and restore data from objects.
Each data model uses its own serializer to ensure safe data storage.

!!! Warning
    Using `pickle` or similar utilities can pose serious security risks.

## Built-in Serializers
### dict

```python
from fastapi_authix.serializers import Serializer

# Remains unchanged, as dict is perfectly suited for JSON conversion.
json = Serializer({"test": "data"}, mode="dict").serialize()
# {"test": "data"}
```

### dataclass
```python
from dataclasses import dataclass

from fastapi_authix.serializers import Serializer

@dataclass(frozen=True)
class TestData:
    name: str

json = Serializer(TestData(name="John"), mode="dataclass").serialize()
# {"name": "John"}

```

### pydantic
```python
from pydantic import BaseModel
from fastapi_authix.serializers import Serializer

class TestModel(BaseModel):
    name: str

json = Serializer(TestModel(name="John"), mode="pydantic").serialize()
# {"name": "John"}
```

### sqlalchemy
```python
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from fastapi_authix.serializers import Serializer

class TestModel(declarative_base()):
    __tablename__ = "test_model"

    id = Column(Integer, primary_key=True)
    name = Column(String)

json = Serializer(TestModel(id=1, name="John"), mode="sqlalchemy").serialize()
# {"id": 1, "name": "John"}
```
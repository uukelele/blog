# tiangolo

**[@tiangolo](https://tiangolo.com/) (Sebastián Ramírez)** is one of my favourite Python developers.

Actually, he probably is my favourite.

Tiangolo has made many things which have made my life easier by about 100 times.

Some of them include:

- [FastAPI](https://fastapi.tiangolo.com/) ✅
- [SQLModel](https://sqlmodel.tiangolo.com/) ✅
- [Typer](https://typer.tiangolo.com/) ✅
- [Asyncer](https://asyncer.tiangolo.com/)
- [Annotated-Doc](https://github.com/fastapi/annotated-doc)

(✅ = I am a regular user)

By far the best is FastAPI. Not because it has the best features (SQLModel and Typer are also very useful), but because of their alternatives.

SQLModel has [sqlalchemy](https://www.sqlalchemy.org/). Typer has [click](https://click.palletsprojects.com/). But FastAPI? The closest competitor is [Flask](https://flask.palletsprojects.com/). But if you want to go into the modern world of <abbr title='Asynchronous Server Gateway Interface'>ASGI</abbr> with **websockets** and **better performance**, you would need the experimental version of it called [Quart](https://quart.palletsprojects.com/). Which has "only" 3k stars. (Yes, it's a lot, but FastAPI has almost 100k, and Flask has over 70k).

But there is one thing in common that brings together all of his projects, and the reason I use them.

!!! info "A slight technicality."
    Well actually there are two things, one of them is that he uses mkdocs-material for all his documentation. But that's not what I'm talking about.

tiangolo always prioritizes <abbr title='Developer Experience. Or DeX. Or whatever you want to call it.'>DX</abbr>.

He always chooses the options that result in the syntax being the cleanest possible.

Look at this:

```python
class Item(SQLModel, table=True):
```

This is an extremely rare case of passing kwargs into a class constructor. In fact, I didn't even know it was possible until I stumbled upon it in the SQLModel docs. Most people would just force the dev to do:

```python
class Item(SQLModel):
    __table__ = True
```

...which is poor DX.

He also adheres to a "stack" of things that most people already use. For example, almost always, **Pydantic** for validation. I love Pydantic. I use it everywhere, I can't resist. I don't like working with web APIs like `data["results"]["items"][0]["url"]`. I would prefer to write my own Pydantic models and then do `data.results.items[0].url`. Because then, I always get my IDE's autocomplete working, and strict type checking.

That brings me onto my next point. tiangolo's projects always love primitive types or Pydantic for more complex ones.

For example, FastAPI. You can type hint a function: `async def search_items(query: str) -> list[Item]`. And then you can start using the query object and the item result. Or you could write a Pydantic model for a JSON payload, then external callers can use the OpenAPI to see they must format their payload like `{"query": "string"}`. But the developer **never** has to think about JSON.

Or another example, Typer. You can write commands and have their arguments be a `pathlib.Path` item. Then Typer automatically converts the argument to a `Path` for you.

In their alternatives, Flask and Click respectively, it takes up so much more boilerplate to do this.

Let me show you:

=== "Flask"

    ```python
    from flask import Flask, request, jsonify
    from pydantic import BaseModel

    class Item(BaseModel):
        url: str
        name: str

    items: list[Item] = [Item(url="http://127.0.0.1", name="localhost")]

    app = Flask(__name__)

    @app.route("/items", methods=["GET"])
    def get_items():
        return jsonify([item.model_dump(mode='json') for item in items])

    @app.route("/items", methods=["POST"])
    def add_item():
        item = request.get_json()

        try:
            items.append(Item(**item))
        except ValidationError as e:
            return {"error": e.errors()}, 400

        return jsonify([item.model_dump(mode='json') for item in items])

    if __name__ == "__main__":
        app.run(debug=True)
    ```

=== "FastAPI"

    ```python
    from fastapi import FastAPI
    from pydantic import BaseModel

    class Item(BaseModel):
        url: str
        name: str

    items: list[Item] = [Item(url="http://127.0.0.1", name="localhost")]

    app = FastAPI()

    @app.get('/items')
    def get_items() -> list[Item]:
        return items

    @app.post('/items')
    def add_item(item: Item) -> list[Item]:
        items.append(item)
        return items
    ```

Surely I don't have to explain much to you to show the difference as night-and-day.

In fact, tiangolo's obsession with perfect DX and type-safety is what inspired me with [ephaptic](https://ephaptic.github.io/ephaptic), an RPC framework of mine which has very much the same design philosphy as FastAPI and integrates with it deeply. I may also in future write an article about ephaptic.

``` python
from enum import Enum
from fastapi import FastAPI, Query, HTTPException, Path
from typing import Optional, Tuple
from datetime import datetime

app = FastAPI()

database = {
    "1":{
        "name":"mina monk",
        "age": "56"
    },
    "2":{
        "name":"montreal impact",
        "age": "30"
    }
}

@app.get("/")
def read_root():
    return {"Hello": "african plan"}

@app.post("/")
def post_root():
    return{"Hello": "african plan.na post method i will send"}

@app.put("/")
def put_root():
    return{"Hello": "african plan.na post method i will send"}

@app.delete("/")
def delete_root():
    return{"Hello": "african plan.na post method i will send"}

@app.patch("/")
def patch_root():
    return{"Hello": "african plan.na post method i will send"}

@app.get("/members")
def get_members():
    return database

@app.get("/members/{member_id}")
def get_member(member_id:str):
    return database[member_id]

class ColorName(str, Enum):
    black = "black"
    pink = "pink"
    purple = "purple"

@app.get("/colors/{color_name}")
def get_color(color_name: ColorName):
    if color_name in ColorName:
        return {"color_name": color_name, "message": "it is fine as heck"}



@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
```


## N0. 1 Write a FastAPI route that accepts a path parameter for user identification
##(user_id) and returns the user's profile information
```python
users = {
    1: {"user_id": 1, "name": "Monk", "age": 27, "email": "monk@gmail.com"},
    2: {"user_id": 2, "name": "Amara", "age": 27, "email": "amara@yahoo.com"},
    3: {"user_id": 3, "name": "Collins", "age": 28, "email": "collins@outlook.com"}
}

@app.get("/users/{user_id}")
def get_user_profile(user_id: int):
    user = users.get(user_id)
    if user:
        return user
    else:
        return {"message": "User not found"}
```
## N0. 2 Implement error handling for the case when the user_id path parameter is
##missing.

```python
users = {
    1: {"user_id": 1, "name": "Monk", "age": 27, "email": "monk@gmail.com"},
    2: {"user_id": 2, "name": "Amara", "age": 27, "email": "amara@yahoo.com"},
    3: {"user_id": 3, "name": "Collins", "age": 28, "email": "collins@outlook.com"},
    4: {"user_id": 4, "name": "Fortune", "age": 25, "email": "fortune@gmail.com"},
    5: {"user_id": 5, "name": "Mirex", "age": 29.5, "email": "moire@yahoo.com"}
}

@app.get("/users1/{user_id}")
def get_user_profile(user_id: int):
    if user_id not in users:
        raise HTTPException(status_code=404, detail="User cannot be found")
    
    return users[user_id]
```

##Create a FastAPI route that accepts query parameters for filtering a list of
##products by category and price range.

```python
products = [
    {"id": 1, "name": "Product A", "category": "electronics", "price": 5000},
    {"id": 2, "name": "Product B", "category": "clothing", "price": 75},
    {"id": 3, "name": "Product C", "category": "Electronics", "price": 20000},
    {"id": 4, "name": "Product D", "category": "Home wares", "price": 1500},
    {"id": 5, "name": "Product E", "category": "Alcohol", "price": 15}
]

@app.get("/products1/")
def get_products(category: Optional[str] = None, price: Optional[float] = None):
    filtered_products = products
    
    # Filter by category if provided
    if category:
        filtered_products = [product for product in filtered_products if product["category"] == category]
      
    
    # Filter by price range if provided
    if price is not None:
        filtered_products = [product for product in filtered_products if product["price"] >= price]
  
    return filtered_products
```

#  Implement default values for the query parameters category defaulting to 'all'
# and price_range defaulting to a specific range.
# Dummy product data

```python
products = [
     {"id": 1, "name": "Product A", "category": "electronics", "price": 5000},
    {"id": 2, "name": "Product B", "category": "clothing", "price": 75},
    {"id": 3, "name": "Product C", "category": "Electronics", "price": 20000},
    {"id": 4, "name": "Product D", "category": "Home wares", "price": 1500},
    {"id": 5, "name": "Product E", "category": "Alcohol", "price": 15}
]

DEFAULT_PRICE_RANGE: Tuple[float, float] = (0, 25000)

@app.get("/products/")
def get_products(category: Optional[str] = 'all', price_range: Tuple[float, float] = Query(DEFAULT_PRICE_RANGE)):
    filtered_products = products
    
    # Filter by category if provided
    if category != 'all':
        filtered_products = [product for product in filtered_products if product["category"] == category]
    
    # Filter by price range if provided
    min_price, max_price = price_range
    filtered_products = [product for product in filtered_products if min_price <= product["price"] <= max_price]
    
    return filtered_products
```

# Write a FastAPI route that accepts a path parameter for city (city_id) and query
#parameters for filtering restaurants by cuisine type (cuisine) and rating (min_rating). and #Ensure that the city ID is a path parameter while cuisine type and minimum rating
#are query parameters.

```python
restaurants = [
    {"id": 1, "name": "Kilimanjaroo", "city_id": 1, "cuisine": "Italian", "rating": 4.5},
    {"id": 2, "name": "Crunchies", "city_id": 2, "cuisine": "Mexican", "rating": 4.2},
    {"id": 3, "name": "FastApi Tasties", "city_id": 3, "cuisine": "Chinese", "rating": 4.8},
    {"id": 4, "name": "Ntachi Osa", "city_id": 4, "cuisine": "Indian", "rating": 4.0},
]

@app.get("/restaurants/{city_id}")
def get_restaurants(city_id: int, cuisine: Optional[str] = None, min_rating: Optional[float] = None):
    filtered_restaurants = [restaurant for restaurant in restaurants if restaurant["city_id"] == city_id]
    
    # Filter by cuisine type if provided
    if cuisine:
        filtered_restaurants = [restaurant for restaurant in filtered_restaurants if restaurant["cuisine"].lower() == cuisine.lower()]
    
    # Filter by minimum rating if provided
    if min_rating is not None:
        filtered_restaurants = [restaurant for restaurant in filtered_restaurants if restaurant["rating"] >= min_rating]
    
    return filtered_restaurants
```

#Modify an existing FastAPI route that accepts a path parameter for user_id to
#ensure that user_id is an integer and greater than zero.

```python
users = {
    1: {"user_id": 1, "name": "Monk", "age": 27, "email": "monk@gmail.com"},
    2: {"user_id": 2, "name": "Amara", "age": 27, "email": "amara@yahoo.com"},
    3: {"user_id": 3, "name": "Collins", "age": 28, "email": "collins@outlook.com"}
}

@app.get("/users2/{user_id}")
def get_user_profile(user_id: int = Path(..., title="User ID", gt=0)):
    user = users.get(user_id)
    if user:
        return user
    else:
        return {"message": "User not found"}
```

##Add validation to a query parameter start_date to ensure it is a valid date format.

```python

def is_valid_date(date_str: str) -> bool:
    try:
        datetime.strptime(date_str, '%Y-%m-%d')
        return True
    except ValueError:
        return False

@app.get("/items/")
async def read_items(start_date: str = Query(..., title="Start Date", description="Date format: YYYY-MM-DD")):
    if not is_valid_date(start_date):
        raise HTTPException(status_code=422, detail="Invalid date format. Date format must be YYYY-MM-DD.")
    
   
    return {"start_date": start_date}
```

#Analyze a given FastAPI application and identify instances where using path
#parameters would be more appropriate than query parameters, and vice versa
# Use Path Parameters When:
# Resource Identification: If the parameter is crucial for identifying a specific resource in the URL hierarchy, such as user profiles, product details, or blog posts, path parameters are more appropriate. For example:
# /users/{user_id}
# /products/{product_id}
# /posts/{post_id}
# Fixed Structure: If the URL structure follows a fixed pattern with identifiable segments representing different resources, path parameters are preferred. For example:
# /categories/{category_id}/products
# /users/{user_id}/orders
# /cities/{city_id}/restaurants
# Use Query Parameters When:
# Optional Filtering: If the parameter is optional and used for filtering or customizing the request, query parameters are more suitable. For example:
# /products?category=electronics
# /posts?author=JohnDoe&sort_by=date
# /search?q=query_string&limit=10
# Sorting and Pagination: If the parameter controls sorting, pagination, or other non-essential options, query parameters are preferred. For example:
# /products?sort_by=price
# /posts?page=2&limit=10
# /search?q=query_string&offset=20&limit=10

##Discuss the benefits of using path and query parameters in the provided FastAPI
##application and how it enhances API design.
# In the provided FastAPI application, the use of both path and query parameters offers several benefits that enhance API design and usability. Here's how each parameter type contributes to the overall effectiveness of the API:

# Benefits of Using Path Parameters:
# Resource Identification: Path parameters are well-suited for identifying specific resources within the API hierarchy. By embedding resource identifiers directly into the URL path, users can intuitively navigate and access the desired resources. For example:
# /users/{user_id}: Retrieves details of a specific user.
# /products/{product_id}: Retrieves details of a specific product.
# /posts/{post_id}: Retrieves details of a specific post.
# Structured URL Paths: Path parameters contribute to a structured and organized URL hierarchy, making it easier for users and developers to understand and navigate the API endpoints. This enhances readability and maintainability.
# SEO Optimization: Well-structured URLs with path parameters can improve search engine optimization (SEO) by making API endpoints more discoverable and understandable to search engines.
# Benefits of Using Query Parameters:
# Customization and Filtering: Query parameters provide flexibility for customizing requests and filtering data based on specific criteria. Users can tailor their requests by providing optional parameters in the query string, enabling them to retrieve the desired subset of data. For example:
# /products?category=electronics: Retrieves products filtered by category.
# /posts?author=JohnDoe&sort_by=date: Retrieves posts authored by John Doe and sorted by date.
# /search?q=query_string&limit=10: Retrieves search results with pagination.
# Optional Parameters: Query parameters are inherently optional, allowing users to include or exclude parameters based on their requirements. This prevents cluttering of the URL and provides a more concise and user-friendly interface.
# Caching and Performance: Query parameters can influence caching behavior by specifying cache keys or cache control directives. By controlling caching at the request level, API performance and scalability can be optimized.
# Enhanced API Design:
# Versatility: By leveraging both path and query parameters, the FastAPI application accommodates a wide range of use cases and user preferences. Whether users need to identify specific resources or customize their requests with filtering and sorting options, the API provides the necessary flexibility.
# Usability: The use of path and query parameters enhances the usability of the API by offering intuitive and customizable endpoints. Users can interact with the API in a natural and efficient manner, resulting in a more satisfying user experience.
# Clarity and Readability: The combination of path and query parameters contributes to a clear and readable API design. Users can easily understand the purpose of each endpoint and the available customization options, leading to improved developer productivity and reduced learning curve.
# In summary, the thoughtful utilization of both path and query parameters in the provided FastAPI application enhances API design by offering versatility, usability, and clarity. By providing intuitive resource identification and customizable request options, the API becomes more adaptable to diverse user needs and promotes a seamless interaction experience.









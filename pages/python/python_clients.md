# Launch a Jupyter notebook with data science libraries loaded

To generate a python client from some OpenApi specifications:
- Need the JSON file with the API Specification
- Use the tool (OpenApi Generator)(https://github.com/OpenAPITools/openapi-generator) to generate the Python Client

To use the client in a Jupyter notebook:
- Copy the libraries to the filesystem where the notebook is running
- Append the path of the library to the system PATH:
```
import sys
sys.path.append("/home/[USER]/library_path")
```

Just import your packages and start using them.




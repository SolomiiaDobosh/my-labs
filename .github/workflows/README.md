name: Check Lab 7 README

on: [push, pull_request]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Check if README.md exists
        run: |
          if [ -f "README.md" ]; then
            echo "OK: README.md found"
          else
            echo "ERROR: README.md not found"
            exit 1
          fi
      
      - name: Check for Mermaid diagram
        run: |
          if grep -q "mermaid" README.md; then
            echo "OK: Mermaid diagram found"
          else
            echo "ERROR: No Mermaid diagram found"
            exit 1
          fi
      
      - name: Check for project structure
        run: |
          if grep -q "Lab" README.md; then
            echo "OK: Project structure found"
          else
            echo "ERROR: No project structure found"
            exit 1
          fi

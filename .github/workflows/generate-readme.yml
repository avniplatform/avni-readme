name: Generate Merged README

on:
  workflow_run:
    workflows: ["Sync Fork"]
    types: [completed]
    branches: [v2.0]
  workflow_dispatch:

jobs:
  generate-readme:
    runs-on: ubuntu-latest
    if: ${{ github.event.workflow_run.conclusion == 'success' || github.event_name == 'workflow_dispatch' }}
    
    steps:
    - name: Checkout
      uses: actions/checkout@v4
      with:
        ref: v2.0
        token: ${{ secrets.GITHUB_TOKEN }}
        
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.9'
        
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install PyYAML
        
    - name: Generate merged README
      run: |
        python << 'EOF'
        import os
        import yaml
        import re
        from pathlib import Path
        
        def read_order_file(directory):
            """Read _order.yaml file and return ordered list"""
            order_file = os.path.join(directory, '_order.yaml')
            if os.path.exists(order_file):
                with open(order_file, 'r', encoding='utf-8') as f:
                    content = yaml.safe_load(f)
                    return content if content else []
            return []
        
        def remove_frontmatter(content):
            """Remove YAML frontmatter from markdown content"""
            if content.startswith('---'):
                parts = content.split('---', 2)
                if len(parts) >= 3:
                    return parts[2].strip()
            return content
        
        def process_directory(directory, level=1):
            """Process directory recursively based on _order.yaml"""
            content = []
            order = read_order_file(directory)
            
            # Process items in order
            for item in order:
                item_path = os.path.join(directory, item)
                
                if os.path.isfile(item_path + '.md'):
                    # It's a markdown file
                    file_path = item_path + '.md'
                    try:
                        with open(file_path, 'r', encoding='utf-8') as f:
                            file_content = f.read()
                            clean_content = remove_frontmatter(file_content)
                            if clean_content.strip():
                                # Add section header based on filename
                                section_name = item.replace('-', ' ').title()
                                content.append(f"{'#' * level} {section_name}\n\n{clean_content}\n")
                    except Exception as e:
                        print(f"Error reading {file_path}: {e}")
                        
                elif os.path.isdir(item_path):
                    # It's a directory, process recursively
                    section_name = item.replace('-', ' ').title()
                    content.append(f"{'#' * level} {section_name}\n\n")
                    subdir_content = process_directory(item_path, level + 1)
                    content.extend(subdir_content)
        
            # Process any remaining .md files not in _order.yaml
            if os.path.exists(directory):
                for file in os.listdir(directory):
                    if file.endswith('.md') and file.replace('.md', '') not in order:
                        file_path = os.path.join(directory, file)
                        try:
                            with open(file_path, 'r', encoding='utf-8') as f:
                                file_content = f.read()
                                clean_content = remove_frontmatter(file_content)
                                if clean_content.strip():
                                    section_name = file.replace('.md', '').replace('-', ' ').title()
                                    content.append(f"{'#' * level} {section_name}\n\n{clean_content}\n")
                        except Exception as e:
                            print(f"Error reading {file_path}: {e}")
            
            return content
        
        # Start generating README
        readme_content = []
        
        # Add main title
        readme_content.append("# Avni Documentation\n\n")
        readme_content.append("This is a comprehensive documentation for Avni, automatically generated from all documentation sources.\n\n")
        readme_content.append("---\n\n")
        
        # Process only docs directory
        if os.path.isdir('docs'):
            dir_content = process_directory('docs', 1)
            readme_content.extend(dir_content)
        
        # Write README.md
        with open('README.md', 'w', encoding='utf-8') as f:
            f.write(''.join(readme_content))
        
        print("README.md generated successfully!")
        EOF
        
    - name: Configure Git
      run: |
        git config user.name "github-actions[bot]"
        git config user.email "github-actions[bot]@users.noreply.github.com"
        
    - name: Commit and push README
      run: |
        git add README.md
        if git diff --staged --quiet; then
          echo "No changes to commit"
        else
          git commit -m "Auto-generate merged README.md from all documentation sources
          
          This README.md file is automatically generated by merging all documentation
          files according to their _order.yaml specifications.
          
          Generated on: $(date -u +'%Y-%m-%d %H:%M:%S UTC')"
          git push origin v2.0
        fi

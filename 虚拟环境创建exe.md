# 虚拟环境的创建

1. python -m venv venv 创建虚拟环境文件夹

2. activate.bat
   
   1. 激活虚拟环境
   
   2. call .\venv\Scripts\activate

3. pip install -r requirement.txt
   
   1. pip freeze
   
   2. requirement.txt 是需要用到的库名

# .exe 文件打包

1. pip install pyinstaller

2. pyinstaller --onefile your_entry_script.py

迁移已有的 PIL 代码到 Pillow
=========================================

Pillow 是对 PIL 的功能增加，想要在 Pillow 下运行 PIL 的代码，只需要：
把这个：:

    import Image

修改成：:

    from PIL import Image

 :py:mod:`_imaging` 模块已经被移除，现在可以这样导入：:

    from PIL.Image import core as _imaging

图像插件导入机制已经改变。Pillow 不会自动导入以 :file:`ImagePlugin.py` 结尾的文件。你需要手动导入。

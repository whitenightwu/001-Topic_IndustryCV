# 得到实例级别的mask结果

detect模型 VS rotate-detect模型
semantic模型（语义分割） VS segment（实例分割）
方案1：先用detect模型得到bbox，然后semantic模型（语义分割）得到
方案2：先用detect模型得到bbox，然后segment（实例分割）得到
方案3：先用rotate-detect模型得到rbbox，然后semantic模型（语义分割）得到
方案4：先用rotate-detect模型得到rbbox，然后segment（实例分割）得到
方案5：直接使用segment（实例分割）得到

最终发现方案5是最简单的、最好的


# 密集场景
人群密度检测


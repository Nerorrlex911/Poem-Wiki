---
sidebar_position: 1
---

# ��ɣ��������

## ָ��

/salt 
  - reload ���ز��
  - opengui <���> <���棺inlay��level��decompose> �򿪽���
  - expansion <���> Ϊ���������Ʒ����

## ����

### config.yml

```yaml
#���ø��ֲ����Ļ���
#��ʽΪ
#ui��:
#  Rune/Enchant:
#    ��������id: "{ calculate '${level} * 2' }" �������Asahi��䣬��֪��ʲô��˼�ճ����� ��''�����ǹ�ʽ����ʽ��${level}�����滻Ϊ�ȼ�
eco:
  decompose:
    Rune:
      eco1: "{ calculate '${level} * 2' }"
      eco2: "{ calculate '${level} * 3' }"
    Enchant:
      eco1: "{ calculate '${level} * 4' }"
      eco2: "{ calculate '${level} * 5' }"
  level:
    Rune:
      eco1: "{ calculate '${level} * 2' }"
      eco2: "{ calculate '${level} * 3' }"
    Enchant:
      eco1: "{ calculate '${level} * 4' }"
      eco2: "{ calculate '${level} * 5' }"
```

### embeddings.yml

```yaml
#������⣬
#��ʯid
�����:
  attributes:
    #1�������ԣ�ע��������"-"
    - - "&7�����˺�: &f+50"
      - "&4��Ѫ: &f+20%"
    #2�������ԣ��Դ�����
    - - "&7�����˺�: &f+60"
      - "&4��Ѫ: &f+25%"
�츽ħ:
  attributes:
    - - "&7�����˺�: &f+50"
      - "&4��Ѫ: &f+20%"
```

## NeigeItems����

### ����ԭ��

NeigeItems�����У�ÿ����Ʒͨ����������ڵ㲢����Ʒ��lore����ʾ����λ�õ��ýڵ�ʵ����Ʒ������ɣ�

�ڵ���������״�ģ�ÿ���ڵ���һ�������ӽڵ㣬�ӽڵ������Ҷ�ӽڵ㣬Ҳ�����ǽڵ㡣��������yaml�ṹ

NeigeItems������Ʒnbt�ϱ����õ��Ľڵ����ݣ�������ͨ��api��ȡ���޸�

ͬʱNeigeItems��������Ʒʱ���Զ�ȡ�ֳɵĽڵ�����

�κ���Salt������ص���Ʒ���ݣ��籦ʯ���ݡ��ȼ������󶨵ȣ����ǽڵ�

�κθ���Salt��Ʒ�Ĳ���������ͨ���޸Ľڵ����ݺ�����������Ʒʵ�ֵ�

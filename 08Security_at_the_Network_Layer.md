# ����㰲ȫ(Security at the Network Layer)��IPSec

- �������Ҫ��ѡ·�Ĺ���

- �����İ�ȫЭ��SSL�������Խ����������
    - SSL����״̬����˶�����״̬������UDP(������Ƶ)û�취��֤��ȫ
        - ����״̬�ĺô����ڰ�ȫ�Ը���(��Ϊ���Բ�ͬ�ĳ����л���ͬ��״̬)���������������ݰ���ͬ�ȶԴ���

    - SSL���㷨��΢����Ч�ʲ���
        - �����ɽ��㷨����Ӳ��ʵ�֣��Ҿ����������㷨(���磺ֻ��Ҫ�ڶ�ϵͳ��ʵ����Կ����������Ҫ�м�ÿ��·������������Կ����)

    - SSL���ܻᵼ�µ��ö˿ڵı仯(https���Լ�ר�ŵĶ˿ڣ��ϲ�Ӧ�ñ�����ѭ�������������SSL��Ҫ�޸ĳ���)
        - ������㰲ȫ����Ӧ�ò���˵��͸���ģ�Ӧ�ò���Ҫ���

- IP���ڴ���ʱͨ�������Ĵ���ģ��ڿͺ����׿����������ݣ�α��Դ��Ŀ���ַ���޸����ݣ��ط����ݰ��ȵȡ�

- �����Ҫ���ⲹ��һ��Э������֤��ȫ
    - IPSec�ṩ�˰�ȫ�ԣ������Ե�һϵ�еı���

## IPSec�ṹ

- �������ݿ⣺
    - ��ȫ�������ݿ⣨Security Policy Database��
    - ��ȫ�������ݿ⣨Security Association Database��

- ����ͨ��Э�飺
    - ��֤�ļ�ͷ��Authentication Header��
    - ��װ��ȫ��Encapsulating Security Payload��

- ����ģʽ��
    - ����ģʽ��Transport Mode��
    - ���ģʽ��Tunnel Mode��

- һ����Կ��������Э�飺
    - ��������Կ������Internet Key Exchange��

## IPSec���������ݿ�(Two Database)

### ��ȫ�������ݿ⣨SPD��

������������Դ��ַ��Ŀ�ĵ�ַ���ϲ�Э�飬�˿ںŵȣ���Щ��Ϣͳ��Ϊtraffic flow������Ψһ��ӳ�䵽һ��SA�ϣ�ʹ������������������������ͬһ�׼��ܷ�ʽ��

- �����յ��İ���һ�����յİ�ȫ�������ݿ�
    - ���ڷ����İ����в�ѯ��ȷ�������
        - �ӵ���drop���������쳣�İ�
        - �������䣨bypass����������IPSec���͵���һ����ͨ�İ����д���
        - Ӧ�ã�apply����ͨ��IPSec��һϵ�д������
            - ��ѯ��ȫ�������ݿ⣨SAD��ȷ��������Ĵ�����
                - �鵽�Ͱ��ն�Ӧ�ķ������д���
                - û�鵽����Ҫͨ����Կ����������ô����

    - �����յ��İ����в�ѯ��ȷ�������
        - �ӵ���drop��������˵����������İ�
        - �������䣨bypass����������IPSec���͵���һ����ͨ�İ����д���
        - Ӧ�ã�apply����ͨ��IPSec��һϵ�д������
        - ��ѯ��ȫ�������ݿ⣨SAD��ȷ��������Ĵ�����
            - �鵽�Ͱ��ն�Ӧ�ķ������д���
            - û�鵽���ӵ������п��ܰ�ȫ֤���Ѿ����ڣ�

#### ���ڷ������Ĵ���

![0](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SPD1.png)

#### ���ڽ��հ��Ĵ���

![1](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/SPD2.png)

### ��ȫ������security association��

��������������ļ��ܷ�ʽ��Э�����ݵȵȡ��������ڲ�ͬʱ������õ��ǲ�ͬ��SA��Ҳ����˵SA����lifetime���Ƶģ����ֶ����õľ�û�С�

- SA��Ҫ�ṩ��ȫ��������Ĳ���
    - ���ҵ����Ҳ����˵����ĳ����ַ�����İ�����ʲô���İ�ȫ��������յ�ĳ����ַ�����İ�����ʲô���İ�ȫ���񣬴Ӷ����Զ����ݰ����к��ʵĴ���

    - ���SA�ǵ����

    - ����SA�����ݻᱻ����ڰ�ȫ�������ݿ⣨SA Database�����淽�����ͨ����ѯ���ݿ�Ϳ��Եõ���Ӧ�ļӽ��ܺ���֤����

    - ��Ҫ��������������������ѯ
        - Security parameter index������ֵ������������ݰ����з���
        - Destination address������ʱ˵����һ���������IPSec���ĵ�ַ��ʲô���յ�ʱ�鿴��ͨ��ʲôԴ��ַ��������
        - Protocol��AHЭ�黹��ESPЭ��

    - ��ѯ���������AH��ESPЭ�������������֤���õ���Կ��ESPЭ����мӽ������õ���Կ��ģʽ�������ڵ�һϵ�еĲ���

### �ܹ�

![2](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Database_Structure.png)

## IPSec��������ȫЭ��(Two Security Protocols)

### ��֤�ļ�ͷ��AH��Э��

- ���ṩ���ܹ��ܣ�ֻ�ṩ���������Ժ�����Դ��֤�Ĺ���

- ���ܿ��������ܾ����ϣ��������õ���ʲô���ܷ������ͷ����طŹ����Ĺ��ܣ�����û�д���ʱ����Ϣ��

![3](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_AH.png)

### ��װ��ȫ��ESP��Э��

- �ṩ�˼��ܵĹ���

- Ҳ���Խ�����������֤

![4](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_ESP.png)

- ��δ���ESPЭ���
    - �ȶ��հ�ȫ�������ݿ⣨security association database���Ի�ȡ���ܵķ����Լ���ϣ�ķ���
    - Ȼ�������������֤
    - ����ٽ��н���

- Ϊʲô��ͷ�����ܣ���β����
    - ��ͷ������SPI��security parameter index���������Ժ�Ͳ�֪����ʲô���ܷ���ȥ������
    - ����β������һЩ��Ҫ��Ϣ������padding��padding�ĳ��ȣ�

## IPSec������ģʽ(Two Modes)

### ����ģʽ��Transport Mode��

- Payload��һ�����ڴ��������
    - ����payload����IPSec��AH/ESPЭ�鴦��
    - ������IP��ͷ��ֻ�������洫�������
        - Ҳ����IP��ͷ�����ķ��͵�

![5](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Transport_Mode.png)

### ���ģʽ��Tunnel Mode��

- Payload��һ��������IP��
    - ����payload����IPSec��AH/ESPЭ�鴦��
    - �����������洫������ݣ�������IP��ͷ
        - ��˿���������ǽ����Ϊ�������ʵĵ�ַ������payload���棬����ǽֻ�ǿ���������ĵ�ַ��

![6](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Tunnel_Mode1.png)

![7](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Tunnel_Mode2.png)

#### ���ַ�ǽ�����Ĳ�ͬ
- VPN��������ȫ�ֵģ�������һ������VPN�����е��������򶼽��Զ�ʹ��VPN(�����)����������SSH�������Ҫ�����趨ʹ������Ż�ʹ���������

- VPN�Ĵ�Ѷȱ�SSH�󣬵���SSH�Ƚ����׷�(���VPN��ǽһ����Ҫ��Ǯ)
    - �а취�����ܾ���ǽ�����SSH����

## ��������Կ����(Internet Key Exchange(IKE))

SA�еļӽ����㷨����Կ��ô����

![8](https://raw.githubusercontent.com/familyld/Network_Security/master/graph/IPSec_Internet_Key_Exchange.png)

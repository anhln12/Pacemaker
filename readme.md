Pacemaker
=========

# Table of Contents
[1. Cluster là gì](#1Cluster là gì)


##1. Cluster là gì
- Cluster là một kiến trúc nhằm đảm bảo nâng cao khả năng sẵn sàng cho các hệ thống mạng máy tính
- Clustering cho phép sử dụng nhiều máy chủ kết hợp với nhau tạo thành một cụm có khả năng chịu đựng hay chấp nhận sai sót (fault-tolerant) nhằm nâng cao độ sẵn sàng của hệ thống mạng.

##2. Mô hình triển khai
- High Performance Cluster
- Load Balancing Cluster
- High Availability Cluster

###2.1 High Performance Cluster (HPC)
- Nhiều máy tính khác nhau làm việc cùng nhau để xử lý một hoặc nhiều tác vụ yêu cầu nhiều tài nguyên tính toán
- Mô hình tổng quan HPC
<img src="http://i.imgur.com/yX8ueM8.png">

###2.2 Load Balancing Cluster (LBC)
- Mỗi máy tính trong cluster sẽ xử lý 1 công việc riêng biệt
- Cần có Load Balancer (Frontend) và Server Farm (Backend)
- Mô hình tổng quan LBC
<img src="http://i.imgur.com/6uVfBLU.png">

###2.3 High Availabality Cluster (HAC)
- Mục đích của kiểu cluster này là đảm bảo các tài nguyên quan trọng sẵn sàng tối đa có thể đạt được
- Mô hình tổng quan HAC
<img src="http://i.imgur.com/IPyBc8z.png">

##3. Lịch sử HAC trong linux
- HA có một lịch sử dài nó bắt đầu vào thập niên 90 của thế kỉ trước và nó là một giải pháp đơn giản với tên Hearbeat.
- Một Hearbeat cluster đơn giản chỉ làm 2 việc:
<ul><li>Monitor 2 nodes và không hơn 2 node</li>
<li>Nó được cấu   hình để start 1 hoặc nhiều dịch vụ trên 2 node</li> </ul>
- Nếu resoure trên node này bị down nó sẽ start resouce trên node còn lại

##4. Các thành phần trong HAC
- các thành phần sau đây đươc sử dụng trong hầu hết clusters.
<ul><li>Shared Storage</li>
<li>Different networks</li>
<li>Bonded networks devices</li>
<li>Multipathing</li>
<li>Fencing/ STONITH device</li></ul>
- Mô hình kiến trúc HA Cluster
<img src="http://i.imgur.com/GSy8kPp.png">
- Resource agents:
<ul><li>Pacemaker là một phần của cluster, có trách nhiệm quản lý các tài nguyên.</li>
<li>Để quản lý tài nguyên, Resource agent được sử dụng.</li>
<li>Một resource agent là một script mà cluster sử dụng để start, stop và monitor resource. Nó có thể so sách systemctl hoặc 1 script chạy có mức độ. Nhưng nó được điều chỉnh để sử dụng trong cluster. Nó cũng có thể định nghĩa các thuộc tính có thể quản lý bởi cluster. Đối với 1 admin, nó rất quan trọng để biết được thuộc tính nào có thể sử dụng trước khi bắt đầu cấu hình resource.</li></ul> 
- Corosync
<ul><li>Corosync là một layer có nhiệm vụ quản lý các node thành viên.</li>
<li>Nó cũng được cấu hình để giao tiếp với pacemaker.</li>
<li>Pacemaker nhận update về những sự thay đổi trạng thái của các node trong cluster. Dựa vào đó nó có thể bắt đầu một sự kiện nào đó ví dụ như migrate resource.</li><ul>
- Storage layer:
<ul><li> Pacemaker cũng được sử dụng để quản lý các thiết bị lưu trữ.</li>
<li>Một quản lý khóa phân phối (Distribute Lock Manage DLM) cần phải có. DLM quản lý việc đồng bộ hóa các khóa giữa các nodes.</li>
<li>Nó đặc biệt quan tronjng nếu là share storage như là cLVM2 cluster logical volumn hoặc GFS2 và OCFS2 cluster file system.</li></ul>

##5. Kiến trúc Pacemaker
<img src="http://i.imgur.com/GTTGMpk.png">

###5.1 Cluster Infomation Base (CIB)
- Trái tim của cluster là Cluster Infomation Base (CIB). Đây là trạng thái thực tế trong bộ nhớ của cluster đó là liên tục đồng bộ giữa các node trong cluster. Bạn sẽ không bao giờ chỉnh sửa trực tiếp được CIB.
- Các CIB sử dụng XML để đại diện cho cả hai cấu hình của cluster và trạng thái hiện tại của tất cả các resource trong cluster. Nội dung của CIB được đồng bộ trên toàn bộ cụm.
- Để hiểu làm thế nào cluster management tool làm việc, hiểu biết CIB tổ chức như nào. Sử dụng lệnh cibadmin -Q

- Ví dụ về một CIB

```
root@ctl1# cibadmin -Q
<cib epoch="500" num_updates="8" admin_epoch="0" validate-with="pacemaker-1.2" crm_feature_set="3.0.7" cib-last-written="Thu Oct 22 16:03:02 2015" update-origin="ctl2" update-client="cibadmin" have-quorum="0" dc-uuid="1">
  <configuration>
    <crm_config>
      <cluster_property_set id="cib-bootstrap-options">
        <nvpair id="cib-bootstrap-options-stonith-enabled" name="stonith-enabled" value="false"/>
        <nvpair id="cib-bootstrap-options-dc-version" name="dc-version" value="1.1.10-42f2063"/>
        <nvpair id="cib-bootstrap-options-no-quorum-policy" name="no-quorum-policy" value="ignore"/>
        <nvpair id="cib-bootstrap-options-cluster-infrastructure" name="cluster-infrastructure" value="corosync"/>
      </cluster_property_set>
    </crm_config>
    <nodes>
      <node id="2" uname="ctl2">
        <instance_attributes id="nodes-2">
          <nvpair id="nodes-2-standby" name="standby" value="off"/>
        </instance_attributes>
      </node>
      <node id="1" uname="ctl1">
        <instance_attributes id="nodes-1">
          <nvpair id="nodes-1-standby" name="standby" value="off"/>
        </instance_attributes>
      </node>
    </nodes>
    <resources>
      <master id="ms_drbd_1">
        <meta_attributes id="ms_drbd_1-meta_attributes">
          <nvpair id="ms_drbd_1-meta_attributes-clone-max" name="clone-max" value="2"/>
          <nvpair id="ms_drbd_1-meta_attributes-notify" name="notify" value="true"/>
          <nvpair id="ms_drbd_1-meta_attributes-interleave" name="interleave" value="true"/>
        </meta_attributes>
        <primitive id="res_drbd_1" class="ocf" provider="linbit" type="drbd">
          <instance_attributes id="res_drbd_1-instance_attributes">
            <nvpair id="nvpair-res_drbd_1-drbd_resource" name="drbd_resource" value="drbd0"/>
          </instance_attributes>
          <operations id="res_drbd_1-operations">
            <op interval="0" id="op-res_drbd_1-start" name="start" timeout="240"/>
            <op interval="0" id="op-res_drbd_1-promote" name="promote" timeout="90"/>
            <op interval="0" id="op-res_drbd_1-demote" name="demote" timeout="90"/>
            <op interval="0" id="op-res_drbd_1-stop" name="stop" timeout="100"/>
            <op id="op-res_drbd_1-monitor" name="monitor" interval="10" timeout="20" start-delay="0"/>
            <op interval="0" id="op-res_drbd_1-notify" name="notify" timeout="90"/>
          </operations>
          <meta_attributes id="res_drbd_1-meta_attributes"/>
        </primitive>
      </master>
      <primitive id="res_Filesystem_1" class="ocf" provider="heartbeat" type="Filesystem">
        <instance_attributes id="res_Filesystem_1-instance_attributes">
          <nvpair id="nvpair-res_Filesystem_1-device" name="device" value="/dev/drbd/by-res/drbd0/0"/>
          <nvpair id="nvpair-res_Filesystem_1-directory" name="directory" value="/mnt/sdb"/>
          <nvpair id="nvpair-res_Filesystem_1-fstype" name="fstype" value="ext4"/>
        </instance_attributes>
        <operations id="res_Filesystem_1-operations">
          <op interval="0" id="op-res_Filesystem_1-start" name="start" timeout="60"/>
          <op interval="0" id="op-res_Filesystem_1-stop" name="stop" timeout="60"/>
          <op id="op-res_Filesystem_1-monitor" name="monitor" interval="20" timeout="40" start-delay="0"/>
          <op interval="0" id="op-res_Filesystem_1-notify" name="notify" timeout="60"/>
        </operations>
        <meta_attributes id="res_Filesystem_1-meta_attributes"/>
      </primitive>
      <primitive id="res_IPaddr2_1" class="ocf" provider="heartbeat" type="IPaddr2">
        <instance_attributes id="res_IPaddr2_1-instance_attributes">
          <nvpair id="nvpair-res_IPaddr2_1-ip" name="ip" value="10.10.10.30"/>
          <nvpair id="nvpair-res_IPaddr2_1-cidr_netmask" name="cidr_netmask" value="255.255.255.0"/>
        </instance_attributes>
        <operations id="res_IPaddr2_1-operations">
          <op interval="0" id="op-res_IPaddr2_1-start" name="start" timeout="20"/>
          <op interval="0" id="op-res_IPaddr2_1-stop" name="stop" timeout="20"/>
          <op id="op-res_IPaddr2_1-monitor" name="monitor" interval="10" timeout="20" start-delay="0"/>
        </operations>
        <meta_attributes id="res_IPaddr2_1-meta_attributes"/>
      </primitive>
      <primitive id="res_apache2_1" class="service" type="apache2">
        <operations id="res_apache2_1-operations">
          <op interval="0" id="op-res_apache2_1-start" name="start" timeout="15"/>
          <op interval="0" id="op-res_apache2_1-stop" name="stop" timeout="15"/>
          <op id="op-res_apache2_1-monitor" name="monitor" interval="15" timeout="15" start-delay="15"/>
        </operations>
        <meta_attributes id="res_apache2_1-meta_attributes">
          <nvpair id="res_apache2_1-meta_attributes-target-role" name="target-role" value="Started"/>
        </meta_attributes>
      </primitive>
    </resources>
    <constraints>
      <rsc_colocation id="col_res_Filesystem_1_ms_drbd_1" score="INFINITY" with-rsc-role="Master" rsc="res_Filesystem_1" with-rsc="ms_drbd_1"/>
      <rsc_order id="ord_ms_drbd_1_res_Filesystem_1" score="INFINITY" first-action="promote" then-action="start" first="ms_drbd_1" then="res_Filesystem_1"/>
      <rsc_colocation id="col_res_IPaddr2_1_res_Filesystem_1" score="INFINITY" rsc="res_IPaddr2_1" with-rsc="res_Filesystem_1"/>
      <rsc_order id="ord_res_Filesystem_1_res_IPaddr2_1" score="INFINITY" first="res_Filesystem_1" then="res_IPaddr2_1"/>
      <rsc_colocation id="col_res_apache2_1_res_IPaddr2_1" score="INFINITY" rsc="res_apache2_1" with-rsc="res_IPaddr2_1"/>
      <rsc_order id="ord_res_IPaddr2_1_res_apache2_1" score="INFINITY" first="res_IPaddr2_1" then="res_apache2_1"/>
      <rsc_colocation id="col_res_haproxy_1_res_IPaddr2_1" score="INFINITY" rsc="res_haproxy_1" with-rsc="res_IPaddr2_1"/>
    </constraints>
    <rsc_defaults>
      <meta_attributes id="rsc-options"/>
    </rsc_defaults>
  </configuration>
  <status>
    <node_state id="1" uname="ctl1" in_ccm="true" crmd="online" crm-debug-origin="post_cache_update" join="member" expected="member">
      <transient_attributes id="1">
        <instance_attributes id="status-1">
          <nvpair id="status-1-probe_complete" name="probe_complete" value="true"/>
          <nvpair id="status-1-last-failure-res_Filesystem_1" name="last-failure-res_Filesystem_1" value="1445501248"/>
          <nvpair id="status-1-master-res_drbd_1" name="master-res_drbd_1" value="10000"/>
        </instance_attributes>
      </transient_attributes>
      <lrm id="1">
        <lrm_resources>
          <lrm_resource id="res_IPaddr2_1" type="IPaddr2" class="ocf" provider="heartbeat">
            <lrm_rsc_op id="res_IPaddr2_1_last_0" operation_key="res_IPaddr2_1_start_0" operation="start" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="42:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;42:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="405" rc-code="0" op-status="0" interval="0" last-run="1445503900" last-rc-change="1445503900" exec-time="441" queue-time="0" op-digest="3be16c7e9769d7f21aafee17a66f0e8a"/>
            <lrm_rsc_op id="res_IPaddr2_1_monitor_10000" operation_key="res_IPaddr2_1_monitor_10000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="43:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;43:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="409" rc-code="0" op-status="0" interval="10000" last-rc-change="1445503900" exec-time="240" queue-time="0" op-digest="46c93acded8a0a6f3a36d547b188ddba"/>
          </lrm_resource>
          <lrm_resource id="res_Filesystem_1" type="Filesystem" class="ocf" provider="heartbeat">
            <lrm_rsc_op id="res_Filesystem_1_last_0" operation_key="res_Filesystem_1_start_0" operation="start" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="39:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;39:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="400" rc-code="0" op-status="0" interval="0" last-run="1445503899" last-rc-change="1445503899" exec-time="187" queue-time="2" op-digest="3a91faf06fce460465231f770779a9bc"/>
            <lrm_rsc_op id="res_Filesystem_1_monitor_20000" operation_key="res_Filesystem_1_monitor_20000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="40:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;40:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="403" rc-code="0" op-status="0" interval="20000" last-rc-change="1445503900" exec-time="165" queue-time="0" op-digest="ac2217b1b44386b997b91f42441910bd"/>
          </lrm_resource>
          <lrm_resource id="res_drbd_1" type="drbd" class="ocf" provider="linbit">
            <lrm_rsc_op id="res_drbd_1_last_0" operation_key="res_drbd_1_promote_0" operation="promote" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="9:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;9:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="394" rc-code="0" op-status="0" interval="0" last-run="1445503899" last-rc-change="1445503899" exec-time="100" queue-time="0" op-digest="1243523f1dae58b4aafa2650a7f3d441"/>
            <lrm_rsc_op id="res_drbd_1_last_failure_0" operation_key="res_drbd_1_monitor_0" operation="monitor" crm-debug-origin="build_active_RAs" crm_feature_set="3.0.7" transition-key="4:33:7:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:8;4:33:7:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="132" rc-code="8" op-status="0" interval="0" last-run="1445501760" last-rc-change="1445501760" exec-time="511" queue-time="0" op-digest="1243523f1dae58b4aafa2650a7f3d441"/>
          </lrm_resource>
          <lrm_resource id="res_apache2_1" type="apache2" class="service">
            <lrm_rsc_op id="res_apache2_1_last_0" operation_key="res_apache2_1_start_0" operation="start" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="43:91:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;43:91:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="429" rc-code="0" op-status="0" interval="0" last-run="1445504582" last-rc-change="1445504582" exec-time="1197" queue-time="0" op-digest="f2317cad3d54cec5d7d7aa7d0bf35cf8"/>
            <lrm_rsc_op id="res_apache2_1_last_failure_0" operation_key="res_apache2_1_monitor_0" operation="monitor" crm-debug-origin="build_active_RAs" crm_feature_set="3.0.7" transition-key="7:69:7:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;7:69:7:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="283" rc-code="0" op-status="0" interval="0" last-run="1445502090" last-rc-change="1445502090" exec-time="42" queue-time="0" op-digest="f2317cad3d54cec5d7d7aa7d0bf35cf8"/>
            <lrm_rsc_op id="res_apache2_1_monitor_15000" operation_key="res_apache2_1_monitor_15000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="44:91:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;44:91:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="432" rc-code="0" op-status="0" interval="15000" last-rc-change="1445504598" exec-time="5" queue-time="15001" op-digest="02a5bcf940fc8d3239701acb11438d6a"/>
          </lrm_resource>
        </lrm_resources>
      </lrm>
    </node_state>
    <node_state id="2" uname="ctl2" in_ccm="false" crmd="offline" crm-debug-origin="post_cache_update" join="down" expected="member">
      <lrm id="2">
        <lrm_resources>
          <lrm_resource id="res_apache2_1" type="apache2" class="service">
            <lrm_rsc_op id="res_apache2_1_last_0" operation_key="res_apache2_1_stop_0" operation="stop" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="47:87:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;47:87:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="75" rc-code="0" op-status="0" interval="0" last-run="1445503884" last-rc-change="1445503884" exec-time="14254" queue-time="0" op-digest="f2317cad3d54cec5d7d7aa7d0bf35cf8"/>
            <lrm_rsc_op id="res_apache2_1_monitor_15000" operation_key="res_apache2_1_monitor_15000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="45:86:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;45:86:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="66" rc-code="0" op-status="0" interval="15000" last-rc-change="1445503881" exec-time="5" queue-time="15003" op-digest="02a5bcf940fc8d3239701acb11438d6a"/>
          </lrm_resource>
          <lrm_resource id="res_IPaddr2_1" type="IPaddr2" class="ocf" provider="heartbeat">
            <lrm_rsc_op id="res_IPaddr2_1_last_0" operation_key="res_IPaddr2_1_stop_0" operation="stop" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="41:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;41:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="88" rc-code="0" op-status="0" interval="0" last-run="1445503898" last-rc-change="1445503898" exec-time="94" queue-time="0" op-digest="3be16c7e9769d7f21aafee17a66f0e8a"/>
            <lrm_rsc_op id="res_IPaddr2_1_monitor_10000" operation_key="res_IPaddr2_1_monitor_10000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="43:86:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;43:86:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="59" rc-code="0" op-status="0" interval="10000" last-rc-change="1445503866" exec-time="398" queue-time="0" op-digest="46c93acded8a0a6f3a36d547b188ddba"/>
          </lrm_resource>
          <lrm_resource id="res_Filesystem_1" type="Filesystem" class="ocf" provider="heartbeat">
            <lrm_rsc_op id="res_Filesystem_1_last_0" operation_key="res_Filesystem_1_stop_0" operation="stop" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="38:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;38:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="96" rc-code="0" op-status="0" interval="0" last-run="1445503898" last-rc-change="1445503898" exec-time="91" queue-time="0" op-digest="3a91faf06fce460465231f770779a9bc"/>
            <lrm_rsc_op id="res_Filesystem_1_monitor_20000" operation_key="res_Filesystem_1_monitor_20000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="40:86:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;40:86:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="53" rc-code="0" op-status="0" interval="20000" last-rc-change="1445503865" exec-time="171" queue-time="0" op-digest="ac2217b1b44386b997b91f42441910bd"/>
          </lrm_resource>
          <lrm_resource id="res_drbd_1" type="drbd" class="ocf" provider="linbit">
            <lrm_rsc_op id="res_drbd_1_last_failure_0" operation_key="res_drbd_1_monitor_0" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="8:77:7:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;8:77:7:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="6" rc-code="0" op-status="0" interval="0" last-run="1445502486" last-rc-change="1445502486" exec-time="222" queue-time="0" op-digest="1243523f1dae58b4aafa2650a7f3d441"/>
            <lrm_rsc_op id="res_drbd_1_last_0" operation_key="res_drbd_1_demote_0" operation="demote" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="11:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;11:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="100" rc-code="0" op-status="0" interval="0" last-run="1445503898" last-rc-change="1445503898" exec-time="74" queue-time="0" op-digest="1243523f1dae58b4aafa2650a7f3d441"/>
            <lrm_rsc_op id="res_drbd_1_monitor_10000" operation_key="res_drbd_1_monitor_10000" operation="monitor" crm-debug-origin="do_update_resource" crm_feature_set="3.0.7" transition-key="13:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" transition-magic="0:0;13:88:0:11ef9dcd-c5f1-4bd1-a368-3d1b1ccc0f17" call-id="112" rc-code="0" op-status="0" interval="10000" last-rc-change="1445503899" exec-time="107" queue-time="0" op-digest="aaa74df15509b944df11eed5ad6096f0"/>
          </lrm_resource>
        </lrm_resources>
      </lrm>
      <transient_attributes id="2">
        <instance_attributes id="status-2">
          <nvpair id="status-2-master-res_drbd_1" name="master-res_drbd_1" value="10000"/>
          <nvpair id="status-2-probe_complete" name="probe_complete" value="true"/>
        </instance_attributes>
      </transient_attributes>
    </node_state>
  </status>
</cib>

```
 


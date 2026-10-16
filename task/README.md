# Lab 007 · Маршрутизация: куда пойдёт пакет

Полноценная работа на двух Linux hosts (pc1/pc2) и двух FRR 10.7.1 routers (r1/r2) в топологии PC1—R1—R2—PC2.

**Сценарии:**
1. Настроить адреса LAN A 10.7.10.0/24, transit 10.7.12.0/30, LAN B 10.7.20.0/24; PC1 и PC2 получают static routes на первый hop.
2. Connected и local routes в FRR (C/L). Отказ при отсутствии удалённых static routes.
3. Заполнить R1 static 10.7.20.0/24 via 10.7.12.2, R2 static 10.7.10.0/24 via 10.7.12.1; ICMP и traceroute в обе стороны.
4. Настоящий PCAP на transit r1:eth2, анализ TTL, ARP и L2-заголовка нового Ethernet-сегмента.
5. **Контролируемая поломка**: R1 static 10.7.20.10/32 Null0 побеждает исправный /24 по longest-prefix match. Capture исходящих Echo Request и отсутствие корректного Reply, диагностика show ip route.
6. Восстановить R1 командой no ip route 10.7.20.10/32 Null0; CHECK сети в обоих направлениях.

**Три PCAP**, создаются при выполнении: captures/routing-before-static.pcap, captures/routing-transit-success.pcap, captures/routing-blackhole.pcap. Два из них на pc1:eth1, второй на r1:eth2. Вывод через PcapViewer.

**Реальные локальные SVG:** task/assets/routing.svg, task/assets/longest-prefix.svg, task/assets/packet-hop.svg. В notebook 6 пар вопрос / точная Markdown ячейка `введите ваш ответ` / review-only AI prompt.

**Безопасность:** учебные eth1/eth2, ни одна команда не меняет management eth0. Проверка маршрутов R1/R2 идёт через FRR vtysh; узлы используют upstream containerlab FRR image со встроенным SSH admin.

Команды для FRR Null0 подтверждены документацией FRR 10.7: https://docs.frrouting.org/en/stable-10.7/static.html

Checker (cms-labs-checker PR #15) проверяет итоговые IPv4 адреса/статические маршруты/отсутствие Null0 и сквозной ping. Он не требует определённого числа ARP или ICMP в PCAP.

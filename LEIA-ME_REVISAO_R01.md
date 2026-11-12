# FTDI232 COM RTS — Revisão R01 — 2026-11-12
Zat Electronic · KiCad 10.0 · padrão Zat (doc 11)

## Arquivos
- FTDI232.* -> FTDI232_COM_RTS.* (nome da pasta).
- Removidos: .history, fp-info-cache, archive_3d_models.log, ERC.rpt, DRC.rpt.
- Removido: schematic-of-ft232rl-usb-to-serial-uart-module-circuit.png (o README não usa).
- .kicad_dru: uma regra, furo-cobre 0,15 mm só dentro do USB-C (J1). É a geometria do USB4105, a mesma regra do DEVKIT V1 PIC18F4550.

## CRÍTICO corrigido (autorizado pelo Kenneth)
- VCCIO (U1 pino 4) estava sem ligação no esquema e no PCB. Sem VCCIO as saídas TXD / DTR / RTS e os LEDs do FT232RL não funcionam.
- Agora VCCIO = VOUT (pino central do JP1). O jumper define o pino VCC do J2 e também o nível lógico da serial (5 V ou 3,3 V).
- PCB: trilha nova F.Cu 0,2 mm, do pad 4 do U1 por baixo do corpo do CI (y = 90,0) até o JP1 pino 2. Clearance ≥ 0,2 mm conferido.

## Esquema
- Folha A4 plana -> capa A2 + 3 folhas: 02 USB-C · ALIMENTAÇÃO, 03 FT232RL · LEDs · JUMPER, 04 CONEXÕES.
- Reanotação: os LEDs L1–L3 viraram D2 (PWR), D3 (TX, CBUS0) e D4 (RX, CBUS1).
  - C1 <-> C2 e C3 <-> C4 trocaram de número (ordem do KiCad).
  - R4 = LED PWR, R5 = TX, R6 = RX.
- Símbolos:
  - J1 -> ZAT_Connector:USB4105GFA (a biblioteca USB4105GFA do projeto não existia).
  - J2 -> ZAT_Connector:Conn_01x06.
  - D1 -> ZAT_Device:SS13.
  - JP1 -> ZAT_Device:Jumper_3_Open: o filtro agora aceita a barra de pinos e os pinos estão no padrão KiCad 10.
- Rótulos globais "+5V" e "GND" viraram símbolos de alimentação.
- Ficaram globais só os labels que passam de uma folha para outra: USB D+/D-, TXD, RXD, DTR, RTS, VOUT. Os outros viraram labels locais.
- Desfeitas as 3 junções de 4 vias (VBUS_OUT, +5V, AGND).
- PWR_FLAG em GND, +5V, VBUS e VOUT.
- No Connect nos pinos livres: SBU1/SBU2, RI, DSR, DCD, CBUS2–4, RESET#, OSCI/OSCO, CTS.
- COM_RTS: o label CTS que sobrava solto no pino 11 saiu; o pino 11 (CTS) recebeu No Connect (entrada com pull-up interno).
- Valores normalizados: 5k1, 1k, 0R, 100nF, 1uF_50V, 10uF_16V, SS1P3L.

## PCB
- Cobre ajustado (autorizado):
  - Via 3V3OUT 0,045 mm para longe da trilha USB D- (clearance 0,19 -> 0,23 mm; furo 0,34 -> 0,38 mm).
  - Via CBUS0 0,025 mm para longe da trilha CBUS1 (furo 0,365 -> 0,39 mm).
  - Pontas de trilha dentro de vias foram centralizadas (track_not_centered_on_via) e os trechos de comprimento zero saíram.
- Zona GND: clearance/espessura mínima 0,2 mm nas duas versões (a COM_CTS estava com 0,3 e deixava o GND do LED PWR ilhado).
  - Salva sem preenchimento: preencher com B.
  - Simulação: todo o GND fica num só grupo.
- J1: ZAT_Connector:USB4105GFA (global). Máscara 0,09 mm nos pads finos (resolve as 11 solder_mask_bridge); atributo through_hole (resolve footprint_type_mismatch).
- J2: ZAT_Connector:PinHeader_1x06_P2.54mm_RightAngle_EdgeMount.
  - Pads iguais aos da barra vertical, serigrafia do lado da borda cortada.
  - 3D da sua barra 90° (${ZAT_3D}/PIN HEADER 90 GRAUS/PinHeader_6X1_right.stp, mesma posição de antes).
- Demais footprints atualizados para o KiCad 10 (SSOP-28: pads deslocados 0,1 mm no comprimento, as trilhas continuam ligadas).
- Logo do verso: courtyard real (B.CrtYd) + "Not in schematic".
- Referências visíveis em F.SilkS 1,0 mm.
- Texto "3.3V / VCC / 5V" 0,2 mm para a esquerda (encostava na serigrafia do JP1).
- Regras da placa: texto mínimo 1,0 / 0,153 mm -> 0,8 / 0,15 mm (padrão doc 05). Resolve 5 text_height e 15 text_thickness sem mexer na arte.

## Biblioteca global (já gravada no PC, md5 conferido)
- symbols/ZAT_Connector.kicad_sym: + Conn_01x06 (filtro ampliado para ZAT_Connector).
- symbols/ZAT_Device.kicad_sym: + Jumper_3_Open (KiCad 10, filtro + PinHeader*1x03*P2.54mm*).
- footprints/ZAT_Connector.pretty: + PinHeader_1x06_P2.54mm_RightAngle_EdgeMount.

## README
- Conferido contra o circuito:
  - J2 é 1 DTR / 2 RXD / 3 TXD / 4 VCC / 5 RTS / 6 GND (estava invertido);
  - JP1: 1-2 = 5 V, 2-3 = 3,3 V (estava invertido);
  - LEDs D2 PWR / D3 TX / D4 RX;
  - C2 é tântalo (não eletrolítico);
  - BOM com os valores certos (R1/R2 5k1, R3 0R, R4–R6 1k, C1/C3 100nF, C4 1uF);
  - nota do VCCIO.

## Pontos de projeto
- MELHORIA: sem proteção ESD nas linhas USB D+/D- (ex.: USBLC6-2SC6).
- MELHORIA: em 3,3 V o pino VCC do J2 vem do regulador interno do FT232RL (~50 mA no máximo).
- Igual à versão COM_CTS, exceto o pino 5 do J2.

## Após abrir no KiCad
1. Reiniciar o KiCad (ZAT_Connector e ZAT_Device atualizadas).
2. ERC no esquema.
3. PCB: B (preencher zonas) -> F8 (deve vir sem alterações) -> DRC.

## R01.1 — 2026-11-12 (erros do ERC/DRC enviados pelo Kenneth)
- ERC pin_not_connected (#FLG2 / #PWR18):
  - Os PWR_FLAG de +5V e VBUS estavam sozinhos na folha CONN, sem nenhum pino dessas nets na folha.
  - Foram para a folha 02 USB_POWER, em cima dos nós reais: +5V no fio D1-K/R4 e VBUS no fio do J1.
  - Na CONN ficam os de GND e VOUT.
- DRC footprint_symbol_mismatch (JP1, BOM): o Jumper_3_Open vinha "in_bom no". A barra de 3 pinos é peça física, então agora está "in_bom yes" no projeto e na ZAT_Device.
- DRC silk_overlap (J2 x textos "USB SERIAL CONVERTER" e "GND/CTS/..."): a serigrafia do J2 ficou só com a marca do pino 1. O corpo da barra 90° está no F.Fab.
- Biblioteca global atualizada (md5 conferido):
  - ZAT_Device:Jumper_3_Open com in_bom yes;
  - ZAT_Connector:PinHeader_1x06_P2.54mm_RightAngle_EdgeMount só com a marca do pino 1.
- Base: os arquivos que você devolveu (com o VBUS movido para 82,55 na CONN e as junções removidas pelo KiCad).

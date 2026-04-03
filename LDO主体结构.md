### LDO主体结构

LDO主体结构包括EA，功率管，补偿网络，反馈网络、超级源随器

其他部分都很好理解，我们来解释一下本设计中最重要的两个部分：补偿网络和超级源随器。

为什么需要源随器呢？

本设计中的负载电容容值为**1μF，容值很大**，所以一定是片外电容LDO。片外电容LDO的主极点是输出极点，请再时稳定性好，重载时稳定性变差。因为重载时负载电流变大，输出电阻变小，主极点就变大，可能讲次级点和零点引入带内，稳定性下降。

我们分析一下次级点。次级点就是EA的输出极点，这里的电阻是EA的输出电阻。由于EA是运放，输出电阻很大。这里的电容是功率管的栅极电容。由于功率管面积很大，所以功率管的栅极电容也很大，这就导致在EA的输出极点处一个大R和一个大C在此结合，就很容易形成一个低频的次级点，影响LDO的稳定性。

解决这个问题的办法是在EA和功率管之间插入一个源随器。源随器的栅极电容小，输出电阻小，这样就可以把一个低频的极点分裂成两个高频的极点。

然而这样的操作也带来一定的问题。在源随器输出极点处，因为功率管的栅极电容很大，就要求源随器的输出电阻很小。而源随器的输出电阻等于
$$
\frac{1}{g_{m2} }
$$
所以就要求源随器的跨导要大。然后源随器的跨导大了，源随器上流过的电流就会增大，会增加LDO的静态功耗（总静态电流不超过5μA）。如果不提升电流，那就要增大W/L，但是增大了W/L，EA输出极点的寄生电容就会增大，又不满足要求。所以这里普通的源随器就无法满足我们的要求了，我们就要寻求更优的设计方案。

Super Source Follower（SSF）超级源随器

<img width="403" height="546" alt="1f6cfb0f2f215ba6b5485c8edcb5ba20" src="https://github.com/user-attachments/assets/bf102531-8351-4577-b5e4-591480f35585" />


基本结构如上，承担源随器任务的是M1管，而M2管的作用是把M1的输出电阻降低M2的本征增益倍。

然而，超级源随器也不够满足我们的追求，毕竟我们的LDO负载电流要在0.1-200mA之间变化，将近两千倍的电流变化使得稳定性非常难调，密勒补偿和SSF将很难维持电路的稳定性。我们在此引入BIA（动态偏置）和电流模补偿。文献是于2007年JSSC上发表的这篇文章。

```
A Transient-Enhanced Low-Quiescent Current Low-Dropout Regulator With Buffer Impedance Attenuation Mohammad Al-Shyoukh, Hoi Lee, Member, IEEE, and Raul Perez, Member, IEEE
```

<img width="1021" height="890" alt="359c1af8bf2c0dacee22838cdde2636d" src="https://github.com/user-attachments/assets/e9d9f041-9c26-4b43-b2a1-0e3daa1f6e94" />


此图为文献中的动态偏置。当负载电流增大时，p2电位下降，M24和M25的栅极电位跟着下降，M23电流增大，从而导致M22电流增大，这样M21的电流也增大，所以M21的跨导变大，从而降低p2处的输出电阻，从而提高p2处极点频率。

<img width="1243" height="757" alt="image-20260403123326684" src="https://github.com/user-attachments/assets/8c70a93c-3707-422a-9219-6332dc54d6b9" />


此图为文献中的电流模补偿。与密勒补偿不同，该补偿接在EA的源极处，通过一个共栅极结构来实现补偿。具体原理可以直接看论文。

<img width="2298" height="968" alt="image-20260403124011142" src="https://github.com/user-attachments/assets/717e470b-b991-4768-9601-6666bfbb3153" />


线性调整率

<img width="2506" height="1548" alt="4872ef5e940e793d5eacfc5c83ea315c" src="https://github.com/user-attachments/assets/1fe72bf7-2bfb-46bf-bac5-825e4b2f7195" />



PSRR

<img width="2506" height="1548" alt="7cfe4f95d5e246795cc2baa288124355" src="https://github.com/user-attachments/assets/007f6b74-33b8-4293-b6c2-cbfecf00e63d" />


负载调整率的图找不到了。

版图没来得及画，估计还有很多要优化学习的地方。

就在这里结束吧。

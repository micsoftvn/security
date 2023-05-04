# Add user can access network interfaces

<pre class="language-txt"><code class="lang-txt"><strong>sudo gpasswd -a yourusername netdev
</strong></code></pre>

netdev : Group have permission to network, you can check other group with this command below

```txt
cat /etc/group
```

Or edit /etc/sudoers with your favorite editor like vim. Add this line under # User privilege specification:

```txt
yourusername ALL=(root) NOPASSWD :/sbin/ifconfig *
```

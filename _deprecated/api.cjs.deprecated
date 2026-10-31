const http = require('http');
const { exec } = require('child_process');
http.createServer((req, res) => {
  res.setHeader('Access-Control-Allow-Origin', '*');
  if (req.url === '/api/health') {
    exec('bash $HOME/twgt-git-hitl/scripts/check-drift.sh', (e, o) => {
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(JSON.stringify({ status: o.includes('[ OK ]') ? 'Healthy' : 'Degraded', logs: o }));
    });
  } else if (req.url === '/api/device') {
    exec('free -m', (e1, o1) => {
      const memLines = o1.split('\n')[1].split(/\s+/);
      exec('df -h /data', (e2, o2) => {
        const diskLines = o2.split('\n')[1].split(/\s+/);
        exec('nproc', (e3, o3) => {
          exec('up time -p', (e4, o4) => {
            exec('termux-battery-status', (e5, o5) => {
              let batt = '0';
              try { batt = JSON.parse(o5).percentage ? JSON.parse(o5).percentage : '0'; } catch(err) {}
              res.writeHead(200, { 'Content-Type': 'application/json' });
              res.end(JSON.stringify({
                memory_used: memLines[2] + 'MB', memory_total: memLines[1] + 'MB',
                storage_used: diskLines[4], cores: o3.trim(),
                kernel: 'Android', uptime: o4.replace('up ', ''),
                device: 'LOCAL NODE', battery: batt + '%'
              }));
            });
          });
        });
      });
    });
  } else if (req.url === '/api/repos') {
    exec('gh repo list lifestyle3nergy-web --limit 200 --json name,description,primaryLanguage,updatedAt', (e, o) => {
      if (e) { res.writeHead(200, { 'Content-Type': 'application/json' }); return res.end('[]'); }
      res.writeHead(200, { 'Content-Type': 'application/json' });
      res.end(o || '[]');
    });
  } else if (req.url === '/api/pipelines') {
    exec('gh pr list -R lifestyle3nergy-web/TWGT- --limit 100 --json state', (e, o) => {
      if (e) { res.writeHead(200, { 'Content-Type': 'application/json' }); return res.end('{"open":0,"merged":0,"blocked":0}'); }
      try {
        const prs = JSON.parse(o);
        const stats = { open: 0, merged: 0, blocked: 0 };
        prs.forEach(pr => {
          if (pr.state === 'OPEN') stats.open++;
          else if (pr.state === 'MERGED') stats.merged++;
          else if (pr.state === 'CLOSED') stats.blocked++;
        });
        res.writeHead(200, { 'Content-Type': 'application/json' });
        res.end(JSON.stringify(stats));
      } catch (err) {
        res.writeHead(500); res.end();
      }
    });
  } else {
    res.writeHead(404);
    res.end();
  }
}).listen(9999, '127.0.0.1', () => console.log('API running on 9999'));
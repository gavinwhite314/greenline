addEventListener('fetch', (event) => {
  event.respondWith(handleRequest(event.request));
});

const LOCALLEADERLIST = [
  { keyword: 'Lieutenant Governor', impact: 'Support the Governor in implementing sustainable efforts and policies.' },
  { keyword: 'Governor', impact: 'Set policies and regulations to promote sustainability.' },
  { keyword: 'Commissioner of Agriculture', impact: 'Shape agricultural practices to prioritize sustainability and environmental conservation.' },
  { keyword: 'Superintendent of Public Instruction', impact: 'Integrate sustainability education into the curriculum.' },
  { keyword: 'Auditor', impact: 'Ensure financial accountability in sustainable initiatives.' },
  { keyword: 'Attorney General', impact: 'Enforce environmental laws and advocate for legal measures to enhance sustainability.' },
  { keyword: 'Commissioner of Labor', impact: 'Promote fair labor practices that align with sustainable principles.' },
  { keyword: 'Treasurer', impact: 'Manage financial resources to support sustainable projects and investments.' },
  { keyword: 'Commissioner of Labor', impact: 'Promote fair labor practices that align with sustainable principles.' },
  { keyword: 'Secretary of State', impact: 'Oversee policies related to sustainability and advocates for international cooperation on environmental issues.' },
  { keyword: 'Mayor', impact: 'Implement local policies and initiatives that contribute to sustainable development.' },
  { keyword: 'Sheriff', impact: 'Enforce local environmental regulations and addresses sustainability concerns within the community.' },

];

async function handleRequest(request) {
  try {
    const loc = new URL(request.url).searchParams.get('loc');
    if (!loc) return new Response("Error: Location Parameter Required, Ex: https://greenline-api.gavinwhite.net/?loc=100%20East%20Main%20St.%2027909", { status: 400 });

    const apiUrl = `https://www.googleapis.com/civicinfo/v2/representatives?key=[HIDDEN KEY]&address=${loc}`;
    const { offices, officials, error } = await (await fetch(apiUrl)).json();

    if (error) return redirectToMain();

    const filteredLeaders = offices.reduce((acc, office) => {
      const positionName = office.name;
      if (isPositionAllowed(positionName)) {
        const { name, phones, channels } = officials[office.officialIndices[0]];
        acc.push({
          name,
          position: positionName,
          phone: phones?.[0] || '',
          socialMedia: channels || [],
          impact: getImpactForPosition(positionName),
        });
      }
      return acc;
    }, []);

    const response = new Response(JSON.stringify(filteredLeaders), {
      headers: {
        'Content-Type': 'application/json',
        'Access-Control-Allow-Origin': '*',
        'Access-Control-Allow-Methods': 'GET, OPTIONS',
        'Access-Control-Allow-Headers': '*',
      },
    });

    return response;
  } catch (error) {
    return redirectToMain();
  }
}

function isPositionAllowed(positionName) {
  return LOCALLEADERLIST.some(item => positionName.toLowerCase().includes(item.keyword.toLowerCase()));
}

function getImpactForPosition(positionName) {
  const LOCALLEADERLISTFILTERED = LOCALLEADERLIST.find(item => positionName.toLowerCase().includes(item.keyword.toLowerCase()));
  return LOCALLEADERLISTFILTERED ? LOCALLEADERLISTFILTERED.impact : '';
}

function redirectToMain() {
  return Response.redirect('http://greenline.gavinwhite.net/', 302);
}
